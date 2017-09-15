# Reflex Arena Dedicated Server Guide Linux


### wine version problem
* Depending on the Reflex version, you may need to use a different version of wine.
* Either **winehq-staging** or **wine**
* To check which version of wine you have installed use the following command : ```wine --version```


## Requirements
* Debian 9
* 1CPU and 1GB of RAM available on your server.


## Installation 1/2 (root part)
### Dependencies
```
dpkg --add-architecture i386
apt-get install -y apt-transport-https
wget -nc https://repos.wine-staging.com/wine/Release.key
apt-key add Release.key
echo deb https://dl.winehq.org/wine-builds/debian/ stretch main >> /etc/apt/sources.list
apt-get update -y
```

### Screen, winehq-staging or wine
#### screen
```apt-get install -y screen```
#### winehq-staging
```apt-get install -y winehq-staging```
#### wine **(only do to replace winehq-staging if necessary)**
```apt-get install -y wine```
You can change version from winehq-staging to wine and vice-versa by using the regular install commands above.
#### Checking currently installed wine version
```wine --version```

### Adding the steam user (if necessary) and logging in
The Reflex files will be installed under the user account "steam" in it's home directory, skip this part if you already have a steam user on your server.
#### Adding the user
```useradd steam -m -r -s /bin/bash```
#### Changing the password (optional)
It can be handy to set a password now to just log in as steam later on.
(If you plan on logging as steam in a ssh session, don't forget to allow ssh password authentication for non root users in your ssh config file)
```echo "steam:oasidqjweqopasdhnoqw" | chpasswd```
#### Logging as steam
```su steam```
```script /dev/null``` 
or ssh steam@your_serversip


## Installation 2/2 (steam part)
