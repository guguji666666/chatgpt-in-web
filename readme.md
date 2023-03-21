## Create ChatGPT-Web
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

#### 5. Apply DNS to your server running CHATGPT service
1. Make sure you have a custom domain
2. Add DNS `A` record in your DNS provider, point `FQDN` to your Azure VM's IP
```
For example,
You bought Domain "abc.com" from DNS provider.
Then you create FQDN "test.abc.com" for your Azure VM.
DNS A record > points "test.abc.com" to the IP of Azure VM.
```
3. How to get your custom domain
* [Get custom domain from Aliyun](https://wanwang.aliyun.com/domain/)

* [Manage your custom domain in Aliyun](https://account.aliyun.com/login/login.htm?oauth_callback=http%3A%2F%2Fdc.console.aliyun.com%2Fnext%2Findex%3Fspm%3D5176.2020520207.recommends.ddomain.606c4c12SpdlTJ#/domain/list/all-domain)

* [Get custom domain from Tecent](https://cloud.tencent.com/act/pro/domain_sales?fromSource=gwzcw.6927084.6927084.6927084&utm_medium=cpc&utm_id=gwzcw.6927084.6927084.6927084&bd_vid=11313871833741623980)

* [Manage your custom domain in Tecent](https://cloud.tencent.com/login?s_url=https%3A%2F%2Fc)

#### 6. Create certificate for your DNS
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
#### 7. Download the cert created
```sh
~/.acme.sh/acme.sh --installcert -d gpt.kjlion.ga  --key-file /home/nginx/certs/key.pem --fullchain-file /home/nginx/certs/cert.pem
```

#### 8. Modify nginx config file
```sh
cd /home/nginx/ && nano nginx.conf
```

```sh
events {

    worker_connections  1024;

}



http {



  client_max_body_size 1000m;  


  #上传限制参数1G以内文件可上传


  server {

    listen 80;

    server_name <Your custom DNS>;

    return 301 https://$host$request_uri;

  }



  server {

    listen 443 ssl http2;

    server_name gpt.kjlion.ga;

    ssl_certificate /etc/nginx/certs/cert.pem;

    ssl_certificate_key /etc/nginx/certs/key.pem;

    location / {

      proxy_pass http://<IP of VPS server>:3002;

      proxy_set_header Host $host;

      proxy_set_header X-Real-IP $remote_addr;

      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;

    }

  }

}
```

#### 9. Start nginx
```sh
docker run -d --name nginx -p 80:80 -p 443:443 -v /home/nginx/nginx.conf:/etc/nginx/nginx.conf -v /home/nginx/certs:/etc/nginx/certs -v /home/nginx/html:/usr/share/nginx/html nginx:latest
```

#### 10. Launch ChatGPT on startup
```sh
docker update --restart=always nginx
```
```sh
docker update --restart=always gpt-app-1
```
