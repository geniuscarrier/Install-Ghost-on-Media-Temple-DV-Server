# Install Ghost on Media Temple DV Server
* To learn more about Ghost, visit [www.ghost.org](https://ghost.org/).
* Assume you have already added your domain (example.com) to Plesk. If not, please follow the [instructions](https://kb.mediatemple.net/questions/710/Add+or+Delete+Domains+and+Domain+Aliases+in+Plesk#dv_40) to do so.
* To get started, connect to your server via SSH as the root user.
* My server info:
```bash
> cat /etc/redhat-release
CentOS release 6.6 (Final)
> python -V
Python 2.6.6
```
## 1. Install Nodejs
### 1a. Install development tools and screen package
```bash
> yum groupinstall "Development Tools"
> yum install screen
```
### 1b. Download Nodejs
```bash
> cd /opt
> wget http://nodejs.org/dist/v0.10.36/node-v0.10.36.tar.gz
```
### 1c. Extract Nodejs
```bash
> tar xzf node-v0.10.36.tar.gz
> cd node-v0.10.36
```
### 1d. Compile Nodejs
```bash
> ./configure
```
### 1e. Install Nodejs
```bah
> make
> make install
```
### 1f. Check Installation
```bash
> node -v
v0.10.36
```
## 2. Install Ghost
Go to working directory
```bash
> cd /var/www/vhosts
```
### 2a. Download Ghost
```bash
> curl -L -O https://ghost.org/zip/ghost-latest.zip
```
### 2b. Extract and Config Ghost
```bash
> unzip -d example.com ghost-latest.zip
> cd example.com
> cp config.example.js config.js
> vim config.js
```
In production section, edit:
```js
url: 'http://example.com'
host: 'YOUR-IP-ADDRESS'
port: '2368'
```
### 2c. Edit Apache VirtualHost Proxy
```bash
> vim ../conf/vhost.conf
```
Add the following code:
```
ProxyPass / http://YOUR-IP-ADDRESS:2368/
ProxyPassReverse / http://YOUR-IP-ADDRESS:2368/
```
### 2d. Install Ghost
```bash
> npm install --production
```
### 2e. Start Ghost
```bash
> npm start --production
```
Go to http://example.com
## 3. Make Ghost Run Forever
### 3a. Install Forever
```bash
> npm -g install forever
```
### 3b. Create the script to start Ghsot
```bash
> vim ghostStart.sh
```
Add
```
#!/bin/bash
NODE_ENV=production forever start index.js
```
### 3c. Create the script to stop Ghost
```bash
> vim ghostStop.sh
```
Add
```
#!/bin/bash
forever stop index.js
```
### 3d. Make the scripts executable for yourself
```bash
> chmod 750 ghostSt*
```
### 3e. Start Ghost
```bash
> ./ghostStart.sh
```
### 3f. Reconfigure Apache and Restart it
```bash
> web
> /etc/init.d/httpd restart
```
If the alis 'web' is not setup run the command below
```bash
> /usr/local/psa/admin/sbin/httpdmng --reconfigure-domain example.com
> /etc/init.d/httpd restart
```
Go to http://example.com
