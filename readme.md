## Create chatgpt in web
#### 1. [Create Azure VM running Linux (Ubuntu 20.04)](https://learn.microsoft.com/en-us/azure/virtual-machines/windows/quick-create-portal)
#### 2. 


#### 5. Create cert for your DNS
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
