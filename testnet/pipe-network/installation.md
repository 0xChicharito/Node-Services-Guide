# Installation

### Node Registration Process <a href="#node-registration-process" id="node-registration-process"></a>

To register for a node, you'll need to generate specific tokens and log in to your account. Follow the steps below:

1.  **Log In to Generate Access Token**

    Open a terminal and execute the command below to log in:



    ```bash
    /opt/dcdn/pipe-tool login --node-registry-url="https://rpc.pipedev.network"
    ```

    This will generate a `credentials.json` file in the `~/.permissionless` directory with your access token. This token proves your identity on the network.
2.  **Generate Registration Token**

    Use the following command to generate a registration token:



    ```bash
    /opt/dcdn/pipe-tool generate-registration-token --node-registry-url="https://rpc.pipedev.network"
    ```

    This will create a file named `registration_token.json` in the `~/.permissionless` directory containing your node registration token, valid for one year. This token is necessary for registering or re-registering nodes.
3. **Authentication Process**
   * Scan the QR code or use the browser window that opens automatically.
   * Create an account or sign in with your Google credentials.
   * Return to the terminal. Upon successful login, you will see the message: “Logged in successfully!”

Your access token will be saved in `~/.permissionless/credentials.json`. Make sure both tokens are present when managing nodes.

### Setup Instructions <a href="#setup-instructions" id="setup-instructions"></a>

#### Download the Node Client Binary <a href="#download-the-node-client-binary" id="download-the-node-client-binary"></a>

1.  **Create Directory:**



    ```bash
    sudo mkdir -p /opt/dcdn
    ```
2.  **Download Pipe tool Binary from URL:**



    ```bash
    sudo curl -L "$PIPE-URL" -o /opt/dcdn/pipe-tool
    ```
3.  **Download Node Binary from URL:**



    ```bash
    sudo curl -L "$DCDND-URL" -o /opt/dcdn/dcdnd
    ```
4.  **Make Binary Executable:**



    ```bash
    sudo chmod +x /opt/dcdn/pipe-tool
    sudo chmod +x /opt/dcdn/dcdnd
    ```

#### Setup the dcdnd node systemd service <a href="#setup-the-dcdnd-node-systemd-service" id="setup-the-dcdnd-node-systemd-service"></a>

To configure the systemd service, follow these steps:

1. **Create the Service File**: Save your service definition in a `.service` file within the `/etc/systemd/system/` directory.
2.  **Sample Service File**:



    ```bash
    # Create service file using cat
    sudo cat > /etc/systemd/system/dcdnd.service << 'EOF'
    [Unit]
    Description=DCDN Node Service
    After=network.target
    Wants=network-online.target

    [Service]
    # Path to the executable and its arguments
    ExecStart=/opt/dcdn/dcdnd \
                    --grpc-server-url=0.0.0.0:8002 \
                    --http-server-url=0.0.0.0:8003 \
                    --node-registry-url="https://rpc.pipedev.network" \
                    --cache-max-capacity-mb=1024 \
                    --credentials-dir=/root/.permissionless \
                    --allow-origin=*

    # Restart policy
    Restart=always
    RestartSec=5

    # Resource and file descriptor limits
    LimitNOFILE=65536
    LimitNPROC=4096

    # Logging
    StandardOutput=journal
    StandardError=journal
    SyslogIdentifier=dcdn-node


    # Working directory
    WorkingDirectory=/opt/dcdn

    [Install]
    WantedBy=multi-user.target
    EOF
    ```

**Optional: Enhanced Security**

For enhanced security, run dcdnd.service under a dedicated service account. This is a more advanced configuration.

```bash
# Create dedicated service account:
sudo useradd -r -m -s /sbin/nologin dcdn-svc-user -d /home/dcdn-svc-user
# Create token subfolder
sudo mkdir -p /home/dcdn-svc-user/.permissionless
sudo chown -R dcdn-svc-user:dcdn-svc-user /home/dcdn-svc-user/.permissionless
# Update the .service file [Service] to include:
User=dcdn-svc-user
Group=dcdn-svc-user
# Update the credentials path under [Service] accordingly:    
--credentials-dir=/home/dcdn-svc-user/.permissionless \
# Move the previously generated registration token
mv /root/.permissionless/registration_token.json /home/dcdn-svc-user/.permissionless
```

#### Ports to open <a href="#ports-to-open" id="ports-to-open"></a>

The ports the user needs to open based on the provided service file are:

1. **Port 8002**: This is for the gRPC server (`--grpc-server-url=0.0.0.0:8002`).
2. **Port 8003**: This is for the HTTP server (`--http-server-url=0.0.0.0:8003`).

**On UFW (Ubuntu/Debian)**:



```bash
sudo ufw allow 8002/tcp
sudo ufw allow 8003/tcp
sudo ufw reload
```

**Additional Notes:**

* Ensure these ports are opened in any cloud provider's network security settings (e.g., AWS Security Groups, Azure Network Security Groups, etc.), if the server is hosted in the cloud.
* Make sure no other service is already using these ports to avoid conflicts.

#### To load, enable and start the new service, follow these steps: <a href="#to-load-enable-and-start-the-new-service-follow-these-steps" id="to-load-enable-and-start-the-new-service-follow-these-steps"></a>

1. **Reload Systemd Daemon**: Use this command to reload the systemd manager configuration

```bash
sudo systemctl daemon-reload
sudo systemctl enable dcdnd
sudo systemctl start dcdnd
```

Configuration and management of the new service are now controlled

#### Recap and explanation: <a href="#recap-and-explanation" id="recap-and-explanation"></a>

The steps outlined above guide you through configuring a systemd service for a DCDN Node Service. By creating a service file in `/etc/systemd/system/`, you define how the service starts and stops, as well as setting necessary parameters like the executable path and network dependencies. After creating the file, you reload the systemd daemon to apply changes, start the service to check its functionality, and enable it to ensure it automatically starts at boot. Lastly, the service's status is verified to confirm it is running as expected.

Follow these steps to manage the state and configuration of the `dcdnd` service

#### Managing the dcdnd Service <a href="#managing-the-dcdnd-service" id="managing-the-dcdnd-service"></a>

**1. View Logs**

To view the logs for the `dcdnd` service in real-time, use:

```bash
sudo journalctl -f -u dcdnd.service
```

**2. Restart Service**

To restart the `dcdnd` service after making configuration changes, execute:

```bash
sudo systemctl restart dcdnd
```

**3. Stop Service**

If you need to stop the `dcdnd` service, use:

```bash
sudo systemctl stop dcdnd
```

**4. Check Status**

To check the current status of the `dcdnd` service, run:

```bash
systemctl status dcdnd
```

#### Pipe Network Wallet Setup & Registration <a href="#pipe-network-wallet-setup-and-registration" id="pipe-network-wallet-setup-and-registration"></a>

#### Log In to Pipe Network <a href="#log-in-to-pipe-network" id="log-in-to-pipe-network"></a>

To log in to the Pipe Network, execute the following command:

```bash
pipe-tool login --node-registry-url="https://rpc.pipedev.network"
```

#### Generate and Register Wallet <a href="#generate-and-register-wallet" id="generate-and-register-wallet"></a>

You can either generate and register a new wallet _or_ link an existing wallet

**Generate a new wallet (Solana Keypair):**

To generate a Solana keypair, run the following command:

```bash
/opt/dcdn/pipe-tool generate-wallet --node-registry-url="https://rpc.pipedev.network"
```

You will be prompted to enter an optional 12-word passphrase **Keypair Location:** By default, the keypair is saved to `~/.permissionless/key.json`. To save the keypair to a different location, specify the desired file path: `--key-path=<path to save the keypair file>` To ensure the security of your wallet, it's crucial to back up your 12-word recovery phrase and key file in a secure location. Failure to do so may result in losing access if the key file or recovery phrase is compromised. When you run the command successfully, you'll receive the 12-word recovery phrase, the public key of the keypair, and the location where the file is stored. The public key is sent to our pipe.network backend as specified by the –node-registry-url

**Linking Your Existing Wallet**

_Alternatively_ you can link your existing wallet’s public address (generated by solana-keygen or other wallets that support Solana) to your account. Two different methods are described:

**Using Base58 Encoded Public Key**

To link your wallet using a base58 encoded public key, run the following command:

```bash
pipe-tool link-wallet --node-registry-url="https://rpc.pipedev.network" --public-key="<base58 encoded public key>"
```

This command will look for the `key.json` file at the default location `~/.permissionless/key.json`. You can specify a custom path to your key file using:

```bash
--key-path="<path to file>"
```

**Linking Wallet Using Keypair File**

To link your wallet's public address with a keypair file, execute:

```bash
/opt/dcdn/pipe-tool link-wallet --node-registry-url="https://rpc.pipedev.network"
```

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-29 002729.png" alt=""><figcaption></figcaption></figure>

#### Conclusion <a href="#conclusion" id="conclusion"></a>

Your node should be functioning at this point, here are a few ways to test it out: 1.&#x20;

```bash
/opt/dcdn/pipe-tool list-nodes --node-registry-url="https://rpc.pipedev.network"
```

<figure><img src="../../.gitbook/assets/Screenshot 2024-11-29 003203.png" alt=""><figcaption></figcaption></figure>

2\. Simply confirm the ports are open and traffic can get to your node over the internet: a) Use a web based port tester to test against your public IP on port 8002 & 8003 OR b) Have a friend at another location try '`telnet <YOUR-PUBLIC-IP> 8002`' and '`telnet <YOUR-PUBLIC-IP> 8003`' to see if an initial connection is made

### Appendix <a href="#appendix" id="appendix"></a>

#### Wallet Utilities <a href="#wallet-utilities" id="wallet-utilities"></a>

**View Private Key**

To view your wallet’s private address, run the following command:

```bash
/opt/dcdn/pipe-tool show-private-key
```

You can also specify a custom key file location using:

```bash
--key-path="<path to key file>"
```

By default, the key file is located at `~/.permissionless/key.json`.

**View Public Key**

To view your wallet’s public address, use the command:

```bash
/opt/dcdn/pipe-tool show-public-key
```

Similarly, you can specify a custom key file location:

```bash
--key-path="<path to key file>"
```

The default location for the key file is `~/.permissionless/key.json`.

**View Linked Wallet**

To view the wallet linked to your account, execute the following command:

```bash
/opt/dcdn/pipe-tool link-wallet --show-linked --node-registry-url="https://rpc.pipedev.network"
```
