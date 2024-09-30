---
hidden: true
cover: broken-reference
coverY: 0
---

# Grafana Dashboard - Monitor node

1\. Install Prometheus

```
cd $HOME
wget https://github.com/prometheus/prometheus/releases/download/v2.42.0/prometheus-2.42.0.linux-amd64.tar.gz
tar xvf prometheus-2.42.0.linux-amd64.tar.gz
sudo mv prometheus-2.42.0.linux-amd64 /opt/prometheus
```

#### # Create Prometheus user <a href="#create-prometheus-user" id="create-prometheus-user"></a>

```
sudo useradd --no-create-home --shell /bin/false prometheus
```

#### # Create directory <a href="#create-directory" id="create-directory"></a>

```
sudo mkdir /etc/prometheus
sudo mkdir /var/lib/prometheus
sudo chown prometheus:prometheus /etc/prometheus
sudo chown prometheus:prometheus /var/lib/prometheus
```

#### # Create Prometheus config <a href="#create-prometheus-config" id="create-prometheus-config"></a>

```
sudo nano /etc/prometheus/prometheus.yml
```

Sample:

```
# Sample config for Prometheus.

global:
  scrape_interval:     15s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).



# Attach these labels to any time series or alerts when communicating with
# external systems (federation, remote storage, Alertmanager).


# Alertmanager configuration
alerting:
  alertmanagers:
  - static_configs:
    - targets: ['localhost:9093']

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

# A scrape configuration containing exactly one endpoint to scrape:
# Here it's Prometheus itself.
scrape_configs:
  # The job name is added as a label `job=<job_name>` to any timeseries scraped from this config.
  - job_name: 'prometheus'

    # Override the global default and scrape targets from this job every 5 seconds.
    scrape_interval: 5s
    scrape_timeout: 5s

    # metrics_path defaults to '/metrics'
    # scheme defaults to 'http'.

    static_configs:
      - targets: ['localhost:9090']

  - job_name: node-exporter
    # If prometheus-node-exporter is installed, grab stats about the local
    # machine by default.
    static_configs:
      - targets: ['IP_VPS_1:9200']
      - targets: ['IP_VPS_2:9200']
      - targets: ['IP_VPS_3:9200']
      - targets: ['IP_VPS_4:9200']
  
  - job_name: '0gchain'
    static_configs:
      - targets: ['localhost:51660']

  - job_name: '0gchain-Contabo'
    static_configs:
      - targets: ['IP_VPS_1:47660']

  - job_name: 'Airchain'
    static_configs:
      - targets: ['IP_VPS_2:19660']

  - job_name: 'Initia'
    static_configs:
      - targets: ['IP_VPS_3:46660']
```

Explain config:

> ```
> job_name: '0gchain'
>     static_configs:
>       - targets: ['localhost:51660']
> ```

For config this port `IP_VPS:51660` you have to edit 2 files at your node `app.toml` & `config.toml`and Restart node

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252F3WLSS09xYJiDRZteF7K2%252FScreen%2520Shot%25202024-07-26%2520at%252019.26.47.png%3Falt%3Dmedia%26token%3D759c13f6-c435-4dac-8d11-e4677bca668d&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=866b2937&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Set permissions for the configuration file

```
sudo chown prometheus:prometheus /etc/prometheus/prometheus.yml
```

#### # Create service file <a href="#create-service-file" id="create-service-file"></a>

```
sudo nano /lib/systemd/system/prometheus.service
```

Content:

```
[Unit]
Description=Monitoring system and time series database
Documentation=https://prometheus.io/docs/introduction/overview/ man:prometheus(1)
After=time-sync.target

[Service]
Restart=on-failure
User=prometheus
EnvironmentFile=/etc/default/prometheus
ExecStart=/usr/bin/prometheus $ARGS --web.listen-address=:9099
ExecReload=/bin/kill -HUP $MAINPID
TimeoutStopSec=20s
SendSIGKILL=no

# systemd hardening-options
AmbientCapabilities=
CapabilityBoundingSet=
DeviceAllow=/dev/null rw
DevicePolicy=strict
LimitMEMLOCK=0
LimitNOFILE=8192
LockPersonality=true
MemoryDenyWriteExecute=true
NoNewPrivileges=true
PrivateDevices=true
PrivateTmp=true
PrivateUsers=true
ProtectControlGroups=true
ProtectHome=true
ProtectKernelModules=true
ProtectKernelTunables=true
ProtectSystem=full
RemoveIPC=true
RestrictNamespaces=true
RestrictRealtime=true
SystemCallArchitectures=native

[Install]
WantedBy=multi-user.target
```

Reload systemd & Start

```
sudo systemctl daemon-reload
sudo systemctl enable prometheus
```

```
sudo systemctl restart prometheus && \
sudo systemctl status prometheus
```

### 2. Install Node Exporter: <a href="#id-2.-install-node-exporter" id="id-2.-install-node-exporter"></a>

**Node Exporter** is a tool that collects hardware and OS-level metrics from Linux systems. It exposes these metrics in a format that Prometheus can scrape, providing insights into system performance and resource usage.

```
cd ~
wget https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
tar xvf node_exporter-1.5.0.linux-amd64.tar.gz
sudo mv node_exporter-1.5.0.linux-amd64/node_exporter /usr/local/bin/
```

#### # Create service file <a href="#create-service-file-1" id="create-service-file-1"></a>

```
sudo nano /etc/systemd/system/node_exporter.service
```

```
[Unit]
Description=Node Exporter
Wants=network-online.target
After=network-online.target

[Service]
User=root
ExecStart=root/node_exporter-1.5.0.linux-amd64/node_exporter --web.listen-address=:9200
Restart=always

[Install]
WantedBy=default.target
```

\*Port listen: 9200 (you can custom) with flag \`--web.listen-address=:9200\`

```
chmod +x root/node_exporter-1.5.0.linux-amd64/node_exporter
```

#### # Reload systemd and start <a href="#reload-systemd-and-start" id="reload-systemd-and-start"></a>

```
systemctl daemon-reload
sudo systemctl enable node_exporter
```

```
systemctl restart node_exporter && \
sudo systemctl status node_exporter
```

### 3. Install Grafana <a href="#id-3.-install-grafana" id="id-3.-install-grafana"></a>

```
sudo apt-get install -y software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana
sudo systemctl enable grafana-server
```

Start & check status

```
sudo systemctl restart grafana-server && \
sudo systemctl status grafana-server
```

Web interface:

```
http://your_server_ip:3000
use:admin
pass:admin
Login & change
```

#### # Grafana config <a href="#grafana-config" id="grafana-config"></a>

Default port is `3000` if you need custom find this line to change to `3002`and restart

```
sudo nano /etc/grafana/grafana.ini
```

```
# The http port  to use
http_port = 3002
```

### 4. Set up Dashboard and Alerting <a href="#id-4.-set-up-dashboard-and-alerting" id="id-4.-set-up-dashboard-and-alerting"></a>

#### # Access your Grafana <a href="#access-your-grafana" id="access-your-grafana"></a>

Launch browser and go to `http://<YOUR-SERVER-GLOBAL-IP>:3000/`

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FWplSkXrbRmffYFpMmFGi%252FScreen%2520Shot%25202024-07-26%2520at%252018.22.56.png%3Falt%3Dmedia%26token%3Da1e56fd9-7c91-4589-9491-1b928c1c41af&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=a67abaf0&#x26;sv=1" alt=""><figcaption></figcaption></figure>

#### # Click Add new data source and select Prometheus <a href="#click-add-new-data-source-and-select-prometheus" id="click-add-new-data-source-and-select-prometheus"></a>

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FS6c3pH614PwoEXtb3A3w%252FScreen%2520Shot%25202024-07-26%2520at%252018.30.42.png%3Falt%3Dmedia%26token%3D16a173cd-c7a9-4d6c-9378-47232ab915f8&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=5bcf179b&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Enter [http://localhost:9099](http://localhost:9090/) into Prometheus server URL

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FdnV2g8zVqCkUF656EgDA%252FScreen%2520Shot%25202024-07-26%2520at%252018.53.50.png%3Falt%3Dmedia%26token%3D03d5bb56-785a-4fb0-8652-3227205ee00a&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=e339660a&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Scroll to the bottom and click Save & test

This port the same port Prometheus service config

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FRUCgoqKX8Ze4W2YlEwMf%252FScreen%2520Shot%25202024-07-26%2520at%252018.55.37.png%3Falt%3Dmedia%26token%3D5e5e7410-3079-47cb-9b55-ee3f4c6366fc&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=e73851f9&#x26;sv=1" alt=""><figcaption></figcaption></figure>

#### # Go to Dashboards to create dashboard <a href="#go-to-dashboards-to-create-dashboard" id="go-to-dashboards-to-create-dashboard"></a>

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FsPiKbEogqQPLMv0aYId6%252FScreen%2520Shot%25202024-07-26%2520at%252019.00.50.png%3Falt%3Dmedia%26token%3D78899999-a01c-4e56-b76e-999b0c059a4f&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=725e2e5&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Click Upload JSON file:

Please download the 0G-grafana-json-file and upload: [https://download.josephtran.co/0g\_validator.json](https://download.josephtran.co/0g\_validator.json)

#### # Select prometheus datasource <a href="#select-prometheus-datasource" id="select-prometheus-datasource"></a>

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252F1M7fPzUggBYdQU245dWv%252FScreen%2520Shot%25202024-07-26%2520at%252019.17.26.png%3Falt%3Dmedia%26token%3D577eae09-5d23-4704-85e0-94512f6f081d&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=885e398d&#x26;sv=1" alt=""><figcaption></figcaption></figure>

#### # Finish <a href="#finish" id="finish"></a>

[\
](https://service.josephtran.xyz/testnet/empe-chain/commands)

<figure><img src="https://service.josephtran.xyz/~gitbook/image?url=https%3A%2F%2F1224718935-files.gitbook.io%2F%7E%2Ffiles%2Fv0%2Fb%2Fgitbook-x-prod.appspot.com%2Fo%2Fspaces%252FS2NilcsT04brh7Bnmj8C%252Fuploads%252FOC3oCy2iNIKcorWgzKOV%252FScreen%2520Shot%25202024-07-28%2520at%252015.41.36.png%3Falt%3Dmedia%26token%3Dee9d0fce-b524-4bfe-a145-44edf6889603&#x26;width=768&#x26;dpr=4&#x26;quality=100&#x26;sign=e1f7e34e&#x26;sv=1" alt=""><figcaption></figcaption></figure>

Source: [https://service.josephtran.xyz/testnet/granfana-dashboard-monitor-node](https://service.josephtran.xyz/testnet/granfana-dashboard-monitor-node)
