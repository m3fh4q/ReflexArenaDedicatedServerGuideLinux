# Reflex Arena Dedicated Server Guide Linux
Reflex Arena is a competitive Arena FPS that combines modern tech with the speed, precision and freedom of a 90s shooter.

__[Steam store page](http://store.steampowered.com/app/328070/Reflex_Arena/) - 
[Official website](https://www.reflexarena.com/) - 
[Forums](http://forums.reflexarena.com/) - 
[Subreddit](https://www.reddit.com/r/reflex/) - 
[Discord](http://discord.gg/reflex#button3)__

<br />
<br />
<br />
<br />

This tutorial will guide you on how to host a/multiple Reflex Arena dedicated servers(s) on linux using wine.

## Table of contents :

* [Requirements](#Requirements)
* [Known problem](#Problem)
* [Installation 1/2 (as root)](#Installation1/2)
* [Installation 2/2 (as steam)](#Installation2/2)
* [Managing the server(s)](#Managing)
* [Serving replays using a webserver](#Serving)
* [Auto replays purge script](#Purge)

<br />
<br />
<br />
<br />

## <a name="Requirements"></a>Requirements
* Debian 9 x64 (what's used in this guide, instructions will probably work on other linux distributions with some slight modifications).
* 1 CPU and 1GB of RAM available on your server.
* I recommend using the $2.50 or $5 offer from [Vultr](https://www.vultr.com/pricing/) (not affiliated with them, it's just what I currently use)
* Reading carefully and understanding what you're doing.

## <a name="Problem"></a>Known problem : wine versions and Reflex Arena builds
* When starting the server, it'll refuse to load if you're using an incompatible wine version.
* Depending on the Reflex Arena build you're using, either __winehq-staging__ or __wine__ will work.
* Change from one to another if necessary (check the wine section below in Installation 1/2).
* To check which version of wine you currently have installed use the following command : 
```
wine --version
```

<br />
<br />
<br />

# <a name="Installation1/2"></a>Installation 1/2 (as root) :
```
su
```
## Install dependencies
### system
```
dpkg --add-architecture i386 && apt-get install -y apt-transport-https && wget -nc https://repos.wine-staging.com/wine/Release.key && apt-key add Release.key && echo deb https://dl.winehq.org/wine-builds/debian/ stretch main >> /etc/apt/sources.list && apt-get update -y
```

### screen
```
apt-get install -y screen
```

### winehq-staging
```
apt-get install -y winehq-staging
```

### _wine_ __(skip it, only do to change from winehq-staging if necessary)__
```
apt-get install -y wine
```

You can change version from winehq-staging to wine and vice-versa by using the regular install commands above, it will overwrite the currently installed wine version.

### Check currently installed wine version (optional, recommended)
```
wine --version
```

## Add the steam user (if necessary)
The Reflex Arena server files will be installed in the "steam" user home directory, server instances will be launched as the steam user.

Skip this part if you already have a steam user on your server.

### Add the user
```
useradd steam -m -r -s /bin/bash
```

### Change the steam user password (optional, recommended)
```
echo "steam:yoursteampassword" | chpasswd
```

(If you plan on logging as steam in a ssh session, don't forget to allow ssh password authentication for non root users in your ssh config file).

<br />
<br />
<br />

# <a name="Installation2/2"></a>Installation 2/2 (as steam) :
```
su steam
``` 
And
```
script /dev/null
```
__Or__ from another machine :

```ssh steam@your_server_ip``` (or use PuTTY)

## Install Server files
### Install steamcmd 
```
cd ~ && wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz && tar -xf steamcmd_linux.tar.gz
```

### Create the steamcmd script to install Reflex Arena
```
printf '@ShutdownOnFailedCommand 1\n@NoPromptForPassword 1\n@sSteamCmdForcePlatformType windows\nlogin anonymous\nforce_install_dir /home/steam/reflex\napp_update 329740 validate\nquit' > reflex.txt
```

### Run steamcmd and run the script (this will install Reflex Arena files)
```
./steamcmd.sh +runscript reflex.txt
```

### Create the replays directory
```
mkdir /home/steam/reflex/replays
```



### Edit dedicatedserver.cfg (optional, not recommended)
```
nano /home/steam/reflex/dedicatedserver.cfg
```  

(Ctrl-O to save, Ctrl-X to exit the editor)

This file contains the server settings that will be applied when you launch the server.
[sv commands list and explanation](http://forums.reflexarena.com/index.php?/topic/4491-is-there-anyway-to-customize-games-beyond-mutators/&do=findComment&comment=36333)
```
more /home/steam/reflex/dedicatedserver.cfg
```

I don't recommend modifying this file as it will be used for all your Reflex Arena server instances running on your server. (You can however duplicate it and have your custom settings there)

It's best to leave this file untouched and use start parameters after the launch command (eg : +sv_hostname m3fh4q Reflex server)

<br />
<br />
<br />

Your Reflex Arena server files are now installed, server instance(s) can be launched !

# <a name="Managing"></a>Managing the server(s)
The server(s) will be fully managed with the steam user, __log in as steam for this section.__
```
su steam
``` 
And
```
script /dev/null
```
## Operations
### Create screen session(s)
```
screen -dmS reflex_server1
``` 

This will create a detached terminal called "reflex_server1"

Each instance of a Reflex dedicated server needs to be launched in a detached terminal using [screen](https://www.gnu.org/software/screen/manual/screen.html), if you launch a server from your current session, it will shutdown when you exit it.

Open as many screen sessions as Reflex Arena dedicated server instances you intend to host on your server, a 1 CPU and 1GB of RAM server can usually handle 2 Reflex server instances with sv_maxclients 8.

### Prepare your server(s) launch settings string
Prepare a string of settings that will follow the launch command, use all the necessary sv_ commands. [sv commands list and explanation](http://forums.reflexarena.com/index.php?/topic/4491-is-there-anyway-to-customize-games-beyond-mutators/&do=findComment&comment=36333)

Example of a string of settings : 

>+sv_hostname m3fh4q Reflex server +sv_steam 1 +sv_autorecord 1 sv_startruleset competitive +sv_starwmap 608558613 +rcon_password myrcon +sv_refpassword myrefpwd +sv_country FR +sv_maxclients 8 +sv_gameport 25787

The most important command is sv_gameport , each Reflex Arena dedicated server instance on your server needs to have a different one, sample : 25787 and 25788 if you have 2 servers, 25789 afterwards etc...

Create your own string of settings and save it somewhere or create a .cfg file in the /home/steam/reflex directory.

### Start the server(s)
The line break with " is important !

Using the example in this guide (replace with your own settings string afer reflexded.exe) :

```
screen -S reflex_server1 -X stuff "cd /home/steam/reflex/ && wineconsole reflexded.exe +sv_hostname m3fh4q Reflex server +sv_steam 1 +sv_autorecord 1 sv_startruleset competitive +sv_starwmap 608558613 +rcon_password myrcon +sv_refpassword myrefpwd +sv_country FR +sv_maxclients 8 +sv_gameport 25787
"
```

Generic :
```
screen -S screen_session_name -X stuff "cd /home/steam/reflex/ && wineconsole reflexded.exe
"
```

__Or (if you're using a seperate custom server cfg file)__

```
screen -S reflex_server1 -X stuff "loadconfig custom_server_cfg
"
```

#### wine version problem

When you start the server, it may not work, this could be due to the current wine version not being compatible with the current Reflex build, in this case, __change from winhq-staging to wine or vice versa__, instructions in the Installation 1/2 section.

### Shutdown the server(s)

Using the example in this guide :
```
screen -S reflex_server1 -X stuff "quit
"
```

Generic :
```
screen -S screen_session_name -X stuff "quit
"
```

### Using the server console
To use the server console, you need to enter the screen session associated with it.

Using the example in this guide :
```
screen -r reflex_server1
```

Generic :
```
screen -r screen_session_name
``` 

Press Ctrl+A and Ctrl+D at the same time to detach from session 

### Update the server(s)
* Shutdown the Reflex Arena dedicated server instance(s) running on your server (Stop the server(s)) using the instructions above.

* Run steamcmd again :  
```
cd ~ && ./steamcmd.sh +runscript reflex.txt
```

### Connect to the server(s)
You can connect to your server using the following command (in your Reflex console)
```
connect yourserverip:port_of_the_instance
``` 

<br />
<br />
<br />

# <a name="Serving"></a>Serving replays using a webserver (optional, recommended)
Replays will be recorded as long as :
* the /home/steam/reflex/replays/ folder is present and has rwx permissions for the steam user.
* server is running sv_autorecord 1

You can setup a web server easily serving the replays folder containing the replays.

If apache2 is already installed and running, just do the configuration and restart part.

All the operations will be done as root. 
```
su
``` 

### Install apache2
```
apt-get install -y apache2
```

### Configure apache2 config file for replays folder
* Open the config file : ```nano /etc/apache2/sites-enabled/000-default.conf```
* (Ctrl-O to save, Ctrl-X to exit the editor)
* Add the following paragraph within the Virtual host declaration :
```
	Alias /replays/ "/home/steam/reflex/replays/"
	<Directory "/home/steam/reflex/replays/">
	Options Indexes FollowSymLinks
	AllowOverride None
	Order allow,deny 
	Allow from all
	Require all granted
	</Directory>
```

* Your 000-default.conf file should look like something like [this](https://pastebin.com/5NYu3zRK)

### Restart apache2
In order to apply the config change, you must restart the webserver !

```
service apache2 restart
```

__Replays from your server (all of your Reflex Arena dedicated server instances) can now be found at the following URL : http://yourserversip/replays/__

<br />
<br />
<br />

# <a name="Purge"></a>Auto replays purge script (optional, recommended)
If you don't want to manually delete replays when there's too many of them, you can setup a script that will be run daily using cron. The following should be done as the steam user. 

```
su steam
```

### Create the script

```
cd ~ && printf 'find /home/steam/reflex/replays/ -mindepth 1 -type f -mtime +60 -print0 | xargs -r0 rm -- –' >> replay_purge.sh && chmod +x replay_purge.sh
```

The +number value in the script is the age threshold after which replay files will be deleted, 60 in the command above.

### Add the script to cron
```
(crontab -l ; echo "* * */1 * * /home/steam/replay_purge.sh") | crontab - -
``` 

The script will be executed once a day.

__Files (Replays) from the /home/steam/reflex/replays/ directory will get deleted if they're older than 60 days.__

<br />
<br />
<br />

[A similar guide for Quake 3 Arena CPMA](https://github.com/m3fh4q/Quake3ArenaCPMADedicatedServerGuideLinux)