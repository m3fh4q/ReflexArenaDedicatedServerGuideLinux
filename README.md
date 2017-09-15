# <center>Reflex Arena Dedicated Server Guide Linux</center>
## Requirements
* Debian 9
* 1 CPU and 1GB of RAM available on your server.

### wine version problem
* Depending on the Reflex version, you may need to use a different version of wine.
* Either **winehq-staging** or **wine**
* To check which version of wine you currently have installed use the following command : ```wine --version```

## Installation 1/2 (root part)
### Dependencies
#### system
```
dpkg --add-architecture i386
apt-get install -y apt-transport-https
wget -nc https://repos.wine-staging.com/wine/Release.key
apt-key add Release.key
echo deb https://dl.winehq.org/wine-builds/debian/ stretch main >> /etc/apt/sources.list
apt-get update -y
```
#### screen
```apt-get install -y screen```
#### winehq-staging
```apt-get install -y winehq-staging```
#### wine **(only do to replace winehq-staging if necessary)**
```apt-get install -y wine```

You can change version from winehq-staging to wine and vice-versa by using the regular install commands above.
#### Checking currently installed wine version
```wine --version```
### Adding the steam user (if necessary)
The Reflex Arena server files will be installed in the "steam" user home directory, server instances will be launched as the steam user.

Skip this part if you already have a steam user on your server.
#### Adding the user
```useradd steam -m -r -s /bin/bash```
#### Changing the password (recommended, optional)
(If you plan on logging as steam in a ssh session, don't forget to allow ssh password authentication for non root users in your ssh config file)

```echo "steam:yoursteampassword" | chpasswd```

### Logging as steam
```su steam``` and when logged in as steam : ```script /dev/null``` 

from another machine : ```ssh steam@your_serversip``` (or use PuTTY)


## Installation 2/2 (steam part)
