# Reflex Arena Dedicated Server Guide Linux
Reflex Arena is a competitive Arena FPS that combines modern tech with the speed, precision and freedom of a 90s shooter.

**[Steam store page](http://store.steampowered.com/app/328070/Reflex_Arena/) - 
[Official website](https://www.reflexarena.com/) - 
[Forums](http://forums.reflexarena.com/) - 
[Subreddit](https://www.reddit.com/r/reflex/) - 
[Discord](http://discord.gg/reflex#button3)**

<br />
<br />

This tutorial will guide you on how to host a/multiple Reflex Arena servers(s) on linux using wine.

<br />
<br />


## Requirements
* Debian 9
* 1 CPU and 1GB of RAM available on your server.

## Known problem : wine versions and Reflex Arena builds
* Depending on the latest Reflex Arena build and wine version at the time of using this guide, you may need to use a different version of wine.
* Either **winehq-staging** or **wine**
* To check which version of wine you currently have installed use the following command : ```wine --version```

<br />
<br />
<br />

# Installation 1/2 (as the root user !) :
```su```
## Install dependencies
### system
```dpkg --add-architecture i386```

```apt-get install -y apt-transport-https```

```wget -nc https://repos.wine-staging.com/wine/Release.key```

```apt-key add Release.key```

```echo deb https://dl.winehq.org/wine-builds/debian/ stretch main >> /etc/apt/sources.list```

```apt-get update -y```

### screen
```apt-get install -y screen```

### winehq-staging
```apt-get install -y winehq-staging```

### wine **(only do to change winehq-staging if necessary)**
```apt-get install -y wine```

You can change version from winehq-staging to wine and vice-versa by using the regular install commands above, it will override the currently installed wine version.

### Check currently installed wine version (optional, recommended)
```wine --version```

### Add the steam user (if necessary)
The Reflex Arena server files will be installed in the "steam" user home directory, server instances will be launched as the steam user.

Skip this part if you already have a steam user on your server.

### Add the user
```useradd steam -m -r -s /bin/bash```

### Change the steam password (optional, recommended)
```echo "steam:yoursteampassword" | chpasswd```

(If you plan on logging as steam in a ssh session, don't forget to allow ssh password authentication for non root users in your ssh config file).

### Login as steam
```su steam``` and when logged in as steam : ```script /dev/null``` 

Or from another machine :
```ssh steam@your_server_ip``` (or use PuTTY)

<br />
<br />
<br />

# Installation 2/2 (as the steam user !) :
```su steam``` and ```script /dev/null```
## Install Server files
### Install steamcmd 
```cd ~ && wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz && tar -xf steamcmd_linux.tar.gz```

### Create the steamcmd script to install Reflex Arena
```printf '@ShutdownOnFailedCommand 1\n@NoPromptForPassword 1\n@sSteamCmdForcePlatformType windows\nlogin anonymous\nforce_install_dir /home/steam/reflex\napp_update 329740 validate\nquit' > reflex.txt```

### Run steamcmd and run the script (this will install Reflex Arena files)
```./steamcmd.sh +runscript reflex.txt```

### Create the replays directory
```mkdir /home/steam/reflex/replays```

Your Reflex Arena server is now installed and ready to be launched.

### edit dedicatedserver.cfg (optional, not recommended)
```nano /home/steam/reflex/dedicatedserver.cfg```  (Ctrl-O to save, Ctrl-X to exit the editor)

This file contains the server settings that will be applied when you launch the server. I recommend reading the file, it countains comments at each setting detailing what it does.
```more /home/steam/reflex/dedicatedserver.cfg```

I don't recommend modifying this file as it will be used for all your Reflex Arena server instances running on your server. (You can however duplicate it and have your custom settings there)

It's best to leave this file untouched and use start parameters after the launch command (eg : +sv_hostname m3fh4q's Reflex server)

<br />
<br />
<br />

# Managing the server(s) (as the steam user !)
```su steam``` and ```script /dev/null```
## Operations
The server(s) can be fully managed with the steam user, **log in as steam for this section**
### Create screen session(s)
```screen -dmS reflex_server1``` will create a detached terminal called "reflex_server1"

Each instance of a reflex server needs to be launched in a detached terminal using [screen](https://www.gnu.org/software/screen/manual/screen.html)

Open as many reflex sessions as Reflex server instances you intend to open, a 1 CPU and 1GB of RAM server can usually handle 2 Reflex server instances with sv_maxclients 8.

### Prepare your server launch settings string
Prepare a string of settings that will follow the launch command, use all the necessary sv_commands (can be found in dedicatedserver.cfg)

Example of a string of settings : 

>+sv_hostname m3fh4q Reflex server +sv_steam 1 +sv_autorecord 1 sv_startruleset competitive +sv_starwmap 608558613 +rcon_password myrcon +sv_refpassword myrefpwd +sv_country FR +sv_maxclients 8 +sv_gameport 25787

The most important command is sv_gameport , each server instance on your server needs to have a different one, sample : 25787 and 25788 if you have 2 servers.

Create your own string of settings and save it somewhere or create a .cfg file in the /home/steam/reflex directory.

### Start the server
The line break with " is important !
```
screen -S screen_session_name -X stuff "cd /home/steam/reflex/ && wineconsole reflexded.exe launch_setting_string
"

```

Using the example in this guide :

```
screen -S reflex_server1 -X stuff "cd /home/steam/reflex/ && wineconsole reflexded.exe +sv_hostname m3fh4q Reflex server +sv_steam 1 +sv_autorecord 1 sv_startruleset competitive +sv_starwmap 608558613 +rcon_password myrcon +sv_refpassword myrefpwd +sv_country FR +sv_maxclients 8 +sv_gameport 25787
"

```

**Or (if you're using a seperate server cfg file)**

```
screen -S reflex_server1 -X stuff "loadconfig custom_server_cfg
"

```

#### wine version problem

When you start the server, it may not work, this could be due to the current wine version not being compatible with the current Reflex build, in this case, **change from winhq-staging to wine or vice versa**, instructions in the Installation 1/2 section.

### Stop the server
```
screen -S screen_session_name -X stuff "quit
"

```

Using the example in this guide :
```
screen -S reflex_server1 -X stuff "quit
"

```

### Update the server(s)
* Shutdown the Reflex instance(s) running on your server (Stop the server(s)) using the instructions above.

* Run steamcmd again : 

```cd ~ && ./steamcmd.sh +runscript reflex.txt```

### Using the server console
To use the server console, you need to enter the screen session associated with it : 
```screen -r screen_session_name``` 

press Ctrl+A and Ctrl+D at the same time to detach from session 

Using the example in this guide :
```screen -r reflex_server1```
