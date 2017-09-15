# Reflex Arena Dedicated Server Guide Linux
Reflex is a competitive Arena FPS that combines modern tech with the speed, precision and freedom of a 90s shooter.

**[Steam store page](http://store.steampowered.com/app/328070/Reflex_Arena/) - 
[Official website](https://www.reflexarena.com/) - 
[Forums](http://forums.reflexarena.com/) - 
[Subreddit](https://www.reddit.com/r/reflex/) - 
[Discord](http://discord.gg/reflex#button3)**

<br />
<br />
<br />
<br />

## Requirements
* Debian 9
* 1 CPU and 1GB of RAM available on your server.

<br />

### Known problem : wine versions and Reflex Arena builds
* Depending on the latest Reflex Arena build and wine version at the time of using this guide, you may need to use a different version of wine.
* Either **winehq-staging** or **wine**
* To check which version of wine you currently have installed use the following command : ```wine --version```

<br />
<br />
<br />

## Installation 1/2 (as root) :
### Install dependencies
#### system
```dpkg --add-architecture i386```

```apt-get install -y apt-transport-https```

```wget -nc https://repos.wine-staging.com/wine/Release.key```

```apt-key add Release.key```

```echo deb https://dl.winehq.org/wine-builds/debian/ stretch main >> /etc/apt/sources.list```

```apt-get update -y```

<br />

#### screen
```apt-get install -y screen```

<br />

#### winehq-staging
```apt-get install -y winehq-staging```

<br />

#### wine **(only do to change winehq-staging if necessary)**
```apt-get install -y wine```

You can change version from winehq-staging to wine and vice-versa by using the regular install commands above, it will override the currently installed wine version.

<br />

#### Check currently installed wine version (optional, recommended)
```wine --version```

<br />
<br />

### Add the steam user (if necessary)
The Reflex Arena server files will be installed in the "steam" user home directory, server instances will be launched as the steam user.

Skip this part if you already have a steam user on your server.

#### Add the user
```useradd steam -m -r -s /bin/bash```

<br />

#### Change the steam password (optional, recommended)
```echo "steam:yoursteampassword" | chpasswd```

(If you plan on logging as steam in a ssh session, don't forget to allow ssh password authentication for non root users in your ssh config file)

<br />
<br />

### Login as steam
```su steam``` and when logged in as steam : ```script /dev/null``` 

**OR**

from another machine : ```ssh steam@your_serversip``` (or use PuTTY)

<br />
<br />
<br />

## Installation 2/2 (as steam) :