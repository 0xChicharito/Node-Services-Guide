---
hidden: true
---

# Create public RPC endpoint

1. **Fetch your IP and RPC port (It will print your RPC url)**

```
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.0gchain/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```

2. **Check if it's responding**

```
curl $RPC/status
```

❗ If you are getting `Connection refused` , you need to make it accessible to the Internet:

1. **Edit config**

```
ed -i '/\[rpc\]/,/\[/{s/^laddr = "tcp:\/\/127\.0\.0\.1:/laddr = "tcp:\/\/0.0.0.0:/}' $HOME/.0gchain/config/config.toml
```

or by manual

2. **Restart your node**

```
sudo systemctl restart ogd
```

3. **Repeat step 2 above, you will see status of log**

```
curl $RPC/status
```

4. **Access domain and create a record**&#x20;

Type: A&#x20;

Subdomain: (e.g. testnet-rpc)

Value: IP of VPS

5. **Check status of subdomain on VPS**

```
nslookup 0g-testnet-rpc.chodefi.com
```

6. **Install Nginx**

```
sudo apt update
sudo apt install nginx
```

```
nginx -v
```

Change \<subdomain.domain> by your subdomain

```
sudo nano /etc/nginx/sites-available/<subdomain.domain>
```

```
server {
    listen 80;
    server_name <subdomain.domain>;

    location / {
        proxy_pass http://localhost:26657;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Next, you need to create a connection from config in sites-available to sites-enabled in order to Nginx can read

```
sudo ln -s /etc/nginx/sites-available/<subdomain.domain> /etc/nginx/sites-enabled/
```

Remove old connection

```
sudo rm /etc/nginx/sites-enabled/<subdomain.domain>
sudo ln -s /etc/nginx/sites-available/<subdomain.domain> /etc/nginx/sites-enabled/
```



Check config

```
sudo nginx -t
sudo systemctl reload nginx
```

Create folder keep the key pair&#x20;

```
sudo mkdir -p /etc/nginx/ssl/
```

```
openssl genpkey -algorithm RSA -out /etc/nginx/ssl/<your_domain_names>.key -pkeyopt rsa_keygen_bits:2048
```

Create CSR (Certificate Signing Request):

```
openssl req -new -key /etc/nginx/ssl/<your_domain_name>.key -out /etc/nginx/ssl/<your_domain_name>.csr
```

Create self-signed certificate from CSR:

```
openssl x509 -req -days 365 -in /etc/nginx/ssl/<your_domain_names>.csr -signkey /etc/nginx/ssl/<your_domain_names>.key -out /etc/nginx/ssl/<your_domain_names>.crt
```

**Register and configure a free SSL certificate from Let's Encrypt on your server:**

Step 1: Install Certbot First, you need to install Certbot, a tool that automates the process of obtaining an SSL certificate from Let's Encrypt. Certbot can be installed on most Linux operating systems via a software package.

For example, on Ubuntu you can install Certbot by running the following commands:

```
sudo apt update
sudo apt install certbot python3-certbot-nginx
```

Step 2: Get an SSL certificate from Let's Encrypt After installing Certbot, you can use it to get an SSL certificate for your domain. To do this, you can run the following command:

```
sudo certbot --nginx -d 0g-testnet-rpc.chodefi.com
```

Step 3: Automatically renew SSL certificate Once you have SSL certificates from Let's Encrypt, you can configure Certbot to automatically renew them each time they expire. You can add a cron job to automatically run Certbot every month, like this:

```
sudo crontab -e
```

It will ask you what to use, choose number 1 as nano and open a file

Then add this line at the end of the file:

```
0 0 1 * * /usr/bin/certbot renew --quiet
```

Open the configuration file again:

```
sudo nano /etc/nginx/sites-available/<your_domain_names>
```

Adjust like below

```
server {
    listen 80;
    server_name <your_domain_names>;

    location / {
        return 301 https://$server_name$request_uri;
    }

}

server {
    listen 443 ssl;
    server_name <your_domain_names>;

    ssl_certificate /etc/nginx/ssl/<your_domain_names>.crt;
    ssl_certificate_key /etc/nginx/ssl/<your_domain_names>.key;

    # Các tùy chọn SSL khác
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDH+AESGCM:ECDH+CHACHA20;
    ssl_ecdh_curve X25519:sect571r1:secp521r1:secp384r1;
    ssl_session_timeout  10m;
    ssl_session_cache shared:SSL:10m;
    ssl_session_tickets off;
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    resolver_timeout 5s;

    location / {
        proxy_pass http://localhost:26657;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}

```

Reload

```
sudo nginx -t
sudo systemctl reload nginx
```

There will be one step that cannot listen to port 443. Then change it to port 8443

Open port 8443

```
sudo iptables -A INPUT -p tcp --dport 8443 -j ACCEPT
```
