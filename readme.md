## Create chatgpt in web
#### 1. [Create Azure VM running Linux (Ubuntu 20.04)](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)
#### 2. Update apt package manager
```
apt update -y  && apt upgrade -y && apt install -y curl wget sudo socat
```
#### 3. Install Docker
```sh
curl -fsSL https://get.docker.com -o get-docker.sh && sh get-docker.sh
```
```sh
curl -L "https://github.com/docker/compose/releases/download/v2.16.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```
```sh
chmod +x /usr/local/bin/docker-compose
```
#### 4. Create directoy for GPT and modify the config file
```sh
cd /home/ && mkdir gpt && cd gpt && nano docker-compose.yml
```
```
services:

  app:

    image: chenzhaoyu94/chatgpt-web

    ports:

      - 3002:3002

    environment:

      OPENAI_API_KEY: <Your API key>
```



#### 5. Create certificate for your DNS
```sh
curl https://get.acme.sh | sh
```
```sh
~/.acme.sh/acme.sh --register-account -m <you mailbox>
```
```sh
~/.acme.sh/acme.sh --set-default-ca --server letsencrypt
```
```sh
~/.acme.sh/acme.sh --issue -d <FQDN of the Azure VM> --standalone
```
#### 6. Download the cert created
```sh
~/.acme.sh/acme.sh --installcert -d gpt.kjlion.ga  --key-file /home/nginx/certs/key.pem --fullchain-file /home/nginx/certs/cert.pem
```

