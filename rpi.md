
### Table of contents

- [About](#about)
- [Docker](#docker)
- [MAC Put OS onto SD card](#mac-put-os-onto-sd-card)
- [Dot Files](#dot-files)
- [MAC Setup](#mac-setup)
- [Git setup](#git-setup)
- [Wifi Setup](#wifi-setup)
- [User Management](#user-management)
- [Setup VNC](#setup-vnc)
- [Setup .local domain](#setup-local-domain)
- [Sudo Preserve Environment Variables](#sudo-preserve-environment-variables)
- [Disk Usage](#disk-usage)
- [GPIO](#gpio)
- [OpenCV Python](#opencv-python)
- [X11 Windows on Mac](#x11-windows-on-mac)
- [Pimoroni Unicorn Hat](#pimoroni-unicorn-hat)
- [Run at startup](#run-at-startup)
- [Symbolic Link](#symbolic-link)
- [SSH-Keys](#ssh-keys)
  - [Overview](#overview)
  - [Details](#details)
  - [SSH-Keys-GITHUB](#ssh-keys-github)
- [Shell](#shell)
- [Cool Stuff](#cool-stuff)
- [TMUX](#tmux)
- [Linux Commands](#linux-commands)
    - [Basic user and shell info](#basic-user-and-shell-info)
    - [Finding Searching](#finding-searching)
    - [Processes](#processes)
    - [ps](#ps)
      - [Columns](#columns)
      - [Top](#top)
      - [cgroups](#cgroups)
    - [Scripts](#scripts)

## About
We have so many Raspberry Pi computers in our household, so we set up a collection of useful information so we can share our knowledge and quicky set up new systems.

## Docker
Install from script and add user, will need to logout for permissions to take efect.

```bash
curl -sSL https://get.docker.com | sh
sudo usermod -aG docker [user_name]
docker run hello-world
```

docker-compose

```bash
sudo pip3 -v install docker-compose
```

## MAC Put OS onto SD card 
Insert SD card  

```bash
diskutil list

/dev/disk0
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *251.0 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:                  Apple_HFS Macintosh HD            250.1 GB   disk0s2
   3:                 Apple_Boot Recovery HD             650.0 MB   disk0s3
/dev/disk1
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:     FDisk_partition_scheme                        *4.0 GB     disk1
   1:                 DOS_FAT_32 NO NAME                 4.0 GB     disk1s1
/dev/disk2
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *39.8 MB    disk2
   1:                  Apple_HFS Garmin Express          39.8 MB    disk2s1
```

Mine is the 4.0 GB and my .img on my system is /Volumes/H/xfer/usefull-apps/RasPi/2014-09-09-wheezy-raspbian.img

```bash
diskutil unmountDisk /dev/disk1
Unmount of all volumes on disk1 was successful


sudo dd bs=1m if=/Volumes/H/xfer/usefull-apps/RasPi/2014-09-09-wheezy-raspbian.img of=/dev/disk1

```

Then wait for a long time CTRL+t will show progress
Took about half an hr. Then plug in the Pi and off you go.

## Dot Files
Clone dot files and make symbolic links example for vim so you are up and running
quickly with your familiar environment
```bash
git clone https://github.com/mirkin/dotfiles.git
ln -s dotfiles/.vimrc .vimrc
```

## MAC Setup
I'm going headless so no monitor, keyboard, or mouse. Plugged into network and checked the
DHCP client table in my router to find it's IP which was 192.168.1.107 in my case.

Open a shell and 

```bash
ssh pi@192.168.1.23

The authenticity of host '192.168.1.107 (192.168.1.107)' can't be established.
RSA key fingerprint is 65:3f:76:ff:1d:5c:02:5f:bb:54:ed:35:d8:b8:5a:f8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.1.107' (RSA) to the list of known hosts.
pi@192.168.1.107's password:
Linux raspberrypi 3.12.28+ #709 PREEMPT Mon Sep 8 15:28:00 BST 2014 armv6l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

NOTICE: the software on this Raspberry Pi has not been fully configured. Please run 'sudo raspi-config'
```
If you want to be up to date

```bash
sudo apt-get update
```

Now configure for the first time

```bash
sudo raspi-config
```

Expand file system to use the full SD
Under advanced set hostname and enable I2C finish and reboot.

##Static IP Setup

Now I want to set a static IP address so change the /etc/network/interfaces 

```bash
auto lo

iface lo inet loopback
iface eth0 inet dhcp

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

Using nano because no vim yet 
ip I want is 192.168.1.32 and my gateway is 192.168.1.1 my DHCP starts
at 192.168.1.100 so no chance it will lease anything under 100 so 32 is safe.

```bash
auto lo

iface lo inet loopback
#iface eth0 inet dhcp
iface eth0 inet static
address 192.168.1.32
netmask 225.225.225.0
network 192.168.1.0
broadcast 192.168.1.255
gateway 192.168.1.1

allow-hotplug wlan0
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface default inet dhcp
```

```bash
sudo reboot
```

Can now ssh to 192.168.1.32

##See Pi Files in Mac Finder

I want to be able to browse and edit files on my Mac so

```bash
sudo apt-get install netatalk
sudo vi /etc/netatalk/afp.conf
```

; are comments to view home dir for any user
```
[Homes]
basedir regex = /home
```

For volumes 
```
[T5]
path = /media/T5
```

Now I can log in at pi and use finder and all my favourite editing tools.


##See Pi files in Windows Explorer

Browse and edit files on the PC.

Install Samba

```bash
sudo apt-get install samba samba-common-bin
```

Then edit /etc/samba/smb.conf

```bash
wins support = yes
[pihome]
   comment= Pi Home
   path=/home/pi
   browseable=Yes
   writeable=Yes
   only guest=no
   create mask=0777
   directory mask=0777
   public=no
```

Set up your user and restart the service

```bash
sudo smbpasswd -a pi
sudo service samba restart
sudo 
```

## Git setup
```bash
git config --global user.email "alex@example.com"
git config --global user.name "My name"
```

## Wifi Setup 
Edit /etc/network/interfaces 

```bash
#interfaces to bring up automatically when ifup is run with -a
auto lo eth0 wlan0

#command such as ifup --allow=hotplug eth0 wlan0 will bring up only if listed below
allow-hotplug eth0
allow-hotplug wlan0

#127.0.0.1
iface lo inet loopback

#iface eth0 inet dhcp

#wired lan gets static ip 192.168.1.32
iface eth0 inet static
   address 192.168.1.32
   gateway 192.168.1.1
   netmask 255.255.255.0
   #network 192.168.1.0
   #broadcast 192.168.1.255

#wireless
iface wlan0 inet manual
wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf
iface walvin inet static
   address 192.168.1.33
   gateway 192.168.1.1
   netmask 255.255.255.0
   #network 192.168.1.0
   #broadcast 192.168.1.255

iface default inet dhcp
```

Edit /etc/wpa_supplicant/wpa_supplicant.conf
You can set multiple wireless networks and have different id_str for each
to reference in /etc/network/interfaces 
``bash
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
   ssid="mywirelessssid"
   psk="mypassword"
   key_mgmt=WPA-PSK
   id_str="walvin"
}
```

##Remove Wolfram

Free up disk space by removing almost 500meg of Wolfram
```bash
sudo apt-get purge wolfram-engine
```

##I2C Setup
Get tools and python libs
```bash
sudo apt-get install i2c-tools
sudo apt-get install python-smbus
```

Add i2c-dev to /etc/modules
```bash
# /etc/modules: kernel modules to load at boot time.
#
# This file contains the names of kernel modules that should be loaded
# at boot time, one per line. Lines beginning with "#" are ignored.
# Parameters can be specified after the module name.

snd-bcm2835
i2c-dev
```
If you have this file, it may need editing
```bash
sudo nano /etc/modprobe.d/raspi-blacklist.conf
```
Then comment out (add # to the start) or remove
```bash
#blacklist i2c-bcm2708
```

Then check with sudo i2cdetect -y 1 (Newer models) or sudo i2cdetect -y 0 depending on your model

```bash
sudo i2cdetect -y 1
     0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
00:          -- -- -- -- -- -- -- -- -- -- -- -- --
10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
30: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
50: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
70: -- 71 -- -- -- -- -- --
```

Any problems, you may need to reboot
```bash
sudo reboot
```

## User Management
Add/Remove a user I'll add and remove alex, the -r is to delete the home folder of alex
```bash
sudo adduser alex
sudo userdel -r alex
```
The user will not be able to sudo, if you want them to be able to then
```bash
sudo visudo
```
and add an entry for them
```bash
#
# This file MUST be edited with the 'visudo' command as root.
#
# Please consider adding local content in /etc/sudoers.d/ instead of
# directly modifying this file.
#
# See the man page for details on how to write a sudoers file.
#
Defaults        env_reset
Defaults        mail_badpass
Defaults        secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"

# Host alias specification

# User alias specification

# Cmnd alias specification

# User privilege specification
root    ALL=(ALL:ALL) ALL

# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# See sudoers(5) for more information on "#include" directives:

#includedir /etc/sudoers.d
pi ALL=(ALL) NOPASSWD: ALL
alex ALL=(ALL) NOPASSWD: ALL
```

Change your password or another person's
```bash
passwd
sudo passwd alex
```

Change autologin from pi to alex
autologin-user=alex in file
```
sudo vi /etc/lightdm/lightdm.conf
```

```
sudo mount -t hfsplus -o remount,rw,force /media/alex/T5
```

## Setup VNC 
```bash
sudo apt-get install tightvncserver
```
Wait for install, and press Y enter if it asks for confirmation.
```bash
vncserver :1

You will require a password to access your desktops.

Password:
```
Follow instructions, and then to start it up
```bash
vncserver :0 -geometry 1920x1080 -depth 24
```

## Setup .local domain
Useful if you get a dynamic IP from your DHCP or even if you have a static IP and forget it. netatalk will already have set this up but here is how to do it anyway.
```bash
sudo apt-get install avahi-daemon
```

## Sudo Preserve Environment Variables
```bash
sudo -E
```

## Disk Usage
Useful to know what is taking up space if you only use a small 4Gig SD.

```bash
sudo du -sh /*
sudo du -sh /dirname/*
```
The h option shows info in “Human Readable Format“ bytes,k,meg,gig instead of disk blocks. The s option will stop it reporting on subdirectories 

## GPIO 
setup pin 8
```bash
sudo echo '8' > /sys/class/gpio/export
```
There will now be pin 8 under /sys/class/gpio
set pin 8 as an output
```bash
sudo echo 'out' > /sys/class/gpio/gpio8/direction
```
set pin 8 to high
```bash
sudo echo '1' > /sys/class/gpio/gpio8/value
```
clean up
```bash
sudo echo '8' > /sys/class/gpio/unexport
```
Permission Denied then use tee
```bash
echo '8' | sudo tee /sys/class/gpio/unexport
```

## OpenCV Python
```bash
sudo apt-get install python-opencv
```

If you want to use the camera
```bash
sudo modprobe bcm2835-v4l2
```

## X11 Windows on Mac
Download X11 for Mac open shell and type xhost +
Tick both boxes on Mac preferences under security
SSH user_of_pi@ip_of_pi -X (I didn't need the -X option)
export DISPLAY='ip_of_mac:0.0'

## Pimoroni Unicorn Hat
Only uses 3 pins GPIO 18, +5V and Ground. To match up with the unicorn hat and pi B+ is used jump wires between pins 2(5V),9(Ground),12(GPIO18) to the same on the Hat. So that is the 1st and 6th on one side and 5th on the other.

Software
https://github.com/pimoroni/UnicornHat
```bash
\curl -sS get.pimoroni.com/unicornhat | bash
```

Or

(Python 3)
sudo apt-get install python3-pip python3-dev
sudo pip-3.2 install unicornhat
(Python 2)
sudo apt-get install python-pip python-dev
sudo pip install unicornhat

## Run at startup
Put file in /etc/init.d (for example start-eyes and make sure it's executable chmod +x start-eyes)
sudo update-rc.d start-eyes defaults

You can follow a pattern to make these startup daemons a little cleaner and allow start and stop
here is an example for a python script that makes LED Eyes light up. 

```bash
#!/bin/bash
#/etc/init.d/led-eyes
### BEGIN INIT INFO
# Provides: led-eyes
# Required-Start:    $remote_fs $syslog
# Required-Stop:     $remote_fs $syslog
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: to start led matrix eyes
# Description:       to start led matrix eyes
### END INIT INFO

# Using the lsb functions to perform the operations.
. /lib/lsb/init-functions
# Process name ( For display )
name=`basename $0`
echo "Name -> $name"
pid_file="/var/run/$name.pid"

case "$1" in
    start)
        log_daemon_msg "Starting LED Eyes"
        start-stop-daemon --start --background --pidfile $pid_file --make-pidfile --exec /home/pi/dev/pi-word-clock/python/Eyes.py
        ;;
    stop)
        log_daemon_msg "Stopping LED Eyes"
        start-stop-daemon --stop --pidfile $pid_file
        ;;
    *)
        echo "usage start-eyes start:stop"
        exit 1
        ;;
esac
exit 0
```

If you want to start/stop this deamon from the shell use a symbolic link so it
can be found on the PATH
```bash
sudo ln -s /etc/init.d/start-eyes /usr/bin/start-eyes
```
####Explanation systemd
Linux kernal PID 0 systemd PID 1 mother of all processes top or ps -e | head
will show if you are using systemd for your init which this description covers.
There are other initialization systems in linux though.  

We have units with name type and config file. 8 types
* automount
* device
* mount
* path
* service
* snapshot
* socket
* target  

A **service unit** is for managing daemons a **target unit** is a group of
other units.  
systemctl list-units  
systemctl list-units | grep .service for just service units  
Config files are  

* /lib/systemd/system units provided by installed packages only run if not a
  file of same name in the other folder  
* /etc/systemd/system units installed by sys admin  

systemctl list-unit-files --type=service  
systemctl list-unit-files --type=target  
A service file for example cat /lib/systemd/system/ssh.service has  

* Description
* After - which units should be activated before this one is started
* Environment File - service's config file  
* ExecStart command used to start this service
* ExecReload command used to reload this service
* WantedBy - **target unit** this service belongs to  

If wanted by contains multi-user.target then when multi-user.target unit is
activated this service will be started.  

To view the units a target unit will activate
```bash
systemctl show --property "Wants" multi-user.target \
| fmt -10 | sed 's/Wants=//g' | sort
```

A **target unit** has *Wants* and *Requires* if a want fails we are cool but if
a requires fails then the entire group of units is deactivated. To find units a
target requires  
systemctl show --property "Requires" multi-user.target  
Look at **target unit** config in this case multi-user.target  
cat /lib/systemd/system/multi-user.target  

* Description
* Requires - if this target activated listed unit also activated. If either is
  deactivated or fails both are.
* Conflicts - Avoids conflicts starting this target stops listed trgets and
  services in this section
* After - units that should be activated before this one
* AllowIsolate - yes or no if yes then only this and it's dependencies are
  activated all others are deactivated
* ExecStart - command to start the service
* ExecReload - command to reload service
* Alias command for systemd to create symbolic link from target unit names
  listed to this unit  
man systemd.service, man systemd.target, man systemd.unit, man -k systemd  
scstemctl status ssh.service see status of service  
Start and Stop services  
systemctl start ssh.service  
systemctl stop ssh.service  
systemctl restart ssh.service  
systemctl condrestart ssh.service conditional restart only restarts if it was
already running  
OK now how to start on boot **persistent services**  
systemctl enable myservice.service to enable and  
systemctl diable myservice.service to disable  
This creates symbolic links  


Add your own service  
add service file in /etc/systemd/system for example Spooky_Eyes.service  
```
[Unit]
Description=Spooky LED Eyes

[Service]
ExecStart=/home/alex/dev/pi-word-clock/python/Eyes.py

[Install]
WantedBy=multi-user.target
```
sudo systemctl enable Spooky_Eyes.service  

## Symbolic Link
```bash
ln -s target source
```

target is the existing link, source is the new symoblic link

##Make Python File Executable
sudo chmod +x filename.py

## SSH-Keys
### Overview
In ~/.ssh are your keys you may need to make the folder if it doesn't exist.
Local client has their private key there, remote server will need the public
key in ~/.ssh/authorized_keys
If you use passphrases ssh-agent can avaid having to keep typing it in.
### Details
To Generate key on client
```bash
ssh-keygen -t rsa -b 4096
```
Copy public key to remote server 
```bash
scp .ssh/id_rsa.pub alex@alexpi32.local:.ssh/new_public_key
```
Log into remote and add it to authorized keys
```bash
cat new_public_key >> authorized_keys
```
If you use a passphrase then ss-agent needs to know so you don't have to keep typing the passphrase in.
```bash
ssh-add ~/.ssh/id_rsa
```
If you get Could not open a connection to your authentication agent
check it's running and try again
```bash
eval `ssh-agent -s`
```

### SSH-Keys-GITHUB
add your id_rsa.pub to github over website under settings
Test
ssh -T git@github.com

Check remote and change to ssh if necessary
git remote -v
git remote set-url origin git@github.com:mirkin/reponame.git


##Search Hackers
Failed logins
sudo cat /var/log/auth.log | grep 'authentication'


## Shell
use vi mode editing in the shell  
set -o vi  


## Cool Stuff
cmatrix  
libaa-bin then aafire  
[figlet for ascii art](README.md#figlet)

## TMUX
tmux ls list sessions
tmux new -s sessionName start new with session name
tmux a #0 attach session 0
tmux a -t sessionName
tmux kill-session -t sessionName
tmux kill-session #0
ctrl-b 
% new pane split vertically
" new pane split horizontally
up/down/left/right move to next pane
; previous pane
q show pane numbers
x kill pane
z zoom toggle
, name window
w list windows
d detatch
t show a clock
[ copy mode press enter to exit (like visual mode in vim)
:resize-p -D 10 resize pane down 10 can use -U -L or -R

##Powerline
Need Source Code Pro for Powerline on iterm.

pip install --user git+git://github.com/Lokaltog/powerline

wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf https://github.com/Lokaltog/powerline/raw/develop/font/10-powerline-symbols.conf
mkdir -p ~/.fonts/ && mv PowerlineSymbols.otf ~/.fonts/
fc-cache -vf ~/.fonts
mkdir -p ~/.config/fontconfig/conf.d/ && mv 10-powerline-symbols.conf ~/.config/fontconfig/conf.d/

##Pandoc convert document formats
sudo apt-get install pandox
sudo apt-get install texlive [large 600megish]

## Linux Commands

#### Basic user and shell info
id will give user group group ids and names  
uname -a type of system you're using  
who -uH (u idle time H show header)  
who am i  
echo $PATH  
alias will list aliases  
type -a cat will tell you where cat command is located -a is to show all  
locations aliases and file location. Shells other than bash use which instead  
of type  
set -o vi sets vi mode for command editing add to .bashrc  
####History
history 7 shows last 2 commands !commandnumber run command at the number  
ctrl-r type to seach reverse command history  
~/.bash_history or ~/.zsh_history is your history file depending on shell  
$HISTFILE /dev/null or $HISTSIZE blank disables history feature.  
####Metachars connecting commands
| pipe output of one command to input of next  
< feed file into stdin eg. wc -l < myfile  
\> redirect to file eg. wc -l myfile.text > outputfile  
\>\> same but append to file  
1> sdout (default)  
2> stderr  
cmd1;cmd2 sequential commands  
command & run in background  
jobs -l show background jobs (l shows ids too)  
%id or fgid bring background job to foreground  
####Expanding commands
use $(command)  
echo "I counted $(ls | wc -w) files and folders"  
arithmetic use $[]  
echo "$[12*12] in a gross"  
####Variables
echo $VARNAME show variable  
env show environment variables  
NAME="hi there" set variable  
export NAME="hi there" variable available to subprocesses  
readonly NAME obvious  
unset NAME obvious  
$PS1 prompt variable  
$SHELL  
$BASH  
$PATH  
####Aliases
alias la='ls -al'
####Bash Config
universal  
/etc/profile sets environment variables then runs scripts in /etc/profile.d  
/etc/bashrc run for bash shell  
per user  
~/.bash_profile good place to put env vars it then runs ~/.bashrc  
~/.bashrc user specific bash stuff aliases and the like  
~/.bash_logout  
####PS1 Prompt
Set color  
\033[38;5;#1;48;5;#2;m where #1 is foreground 0-255 colour code #2 background   
Unicode Chars  
PS1=$'\ue0b0'  
####Permissions
-rwxrwxrwx+ 1st char - normal d folder l symbolic link s socket p named pipe  
then user group everyone permissions read write execute + ACL set see below  
chmod 754 3 bit digit for user group everyone 4=r 2=w 1=x example shows  
rwxr-x-r-  
chmod -R recursive  
Can also use letters u=user g=group o=other a=all +=allow -=deny  
chmod a+rwx give everyone full permission  
chmod o-x don't let others execute  
cgmod ug+wx let user and group write and execute  
chown -R user:group thing changes user and group recursively -R and :group is  
optional  
Actually 754 is really 0754 as there is something before user/group/other the 3
bit digit for that 4=set userID bit (u+s) 2=set group bit (g+s) 1=sticky bit
o+t  
If group ID (2 or g+s) is set in a directory then when a user makes a new file,
instead of having it's group set to the group of that user, it is set to the
group of the owner of the directory. Even if that user doesn't belong to that
group (so long as others can write). While this new files is executing it's
owner is the file's group not the user who is executing it.  
If user ID is set (4 or u+s) it will execute as it's owners user rather then
the user executing it.

#####Access Contol Lists
ls -al + listed in presmissions shows ACL  
getfacl filename  
setfacl -m u:username:rwx filename  
setfacl -m g:groupname:rw filename  
setfacl -x u:username filename remove ACL entry for username  
NOTE MASK even if user permissions exceed those in mask they will be limited
you get a #effective in getfacl you'd need a chmod to correct this.  
setfacl d:g:kids:r /importantstuff sets dafault ACL on directory which will be
inherited. files won't be executable automatically as files are not by default  
####Move Copy Delete
mv  
cp  
rm  
cp -a maintain timestamps and permissions in copy  
-r recursive  
#### Finding Searching
locate or find?  
locate uses a DB config in /etc/updatedb.conf hence fast but won't find
anything not in the DB since updatedb last run.

locate lostfile will seach our a file, didn't come as standard on raspbian so
had to install sudo apt-get install mlocate then sudo updatedb (mlocate is
newer than locate)

find /path will list all files and directories under path  
find /path1 /path2 search multiple paths  
find /path -ls gives a long listing for extra details  
find /path -name needle filters for just needle in filename  
find /path -iname needle case insensitive  
Annoying permission denied? redirect errors to /dev/null  
find / -name '*cool*' 2> /dev/null  
find / -type d -iname "projects" -ls find directory  
find / -type f find just files  
find by size - smaller + bigger or none for exact gig/k/meg/bytes G/M/k/c  
find / -size +10M  
find / -size +500M -size -5G  
find by user or group  
find / -user alex -or -user clare  
find / -not -group kids  
find by permisssions  
find /path -perm 777 finds rwxrwxrwx  
find /path -perm -644 at least 644 (777,776,755,754,744,666,655,654,644) -  
means you need 644 but can have extra permissions too  
find /path  -perm /711 at least one (owner/group/other) has the permission  
described kind of like and or for each.  
find by date and time prefix letter a/m/c accessed/modified/metadata changed  
metadata is owner,group,permissions,file size, time stamp etc. Also prefix -  
for has been changed and + for hasn't been changed or nothing for exact match  
time for days, min for minutes  
find /path -atime -30 find accessed 30 days ago  
find /path -amin +10 find not accessed in last 10 minutes  
find /path -ctime -3 find meta data changed in past 3 days  
Boolean searches  
find /path \( -user alex -o -user pi \) user alex or pi  
find /path -user alex -and -size +5M  
find /path -user alex -not -group gpio all owned by alex but not in group gpio  
Execute Commands with each result from find  
-exec won't ask -ok will use {} in place of each of the found files. To end the  
-exec or -ok use \;  
find /path -iname *cool* -exec echo "{} is found" \;  
find /path -user alex -ok cp {} /home/backup/ \;  
find /path -size +100M -exec du {} \; | sort nr  

Searching using G\RE\P globally search regular expression and print  
alias grep='grep --color=auto' to show matches in colour  
grep thing /path/file searches for thing in file case sensitive returns lines  
in files matching thing  
grep -i thing /path/file case insensitive  
grep -v thing /path/file lines that don't contain thing  
grep -r thing /path recursive search directory not just one file  
grep -rl shows files containing matches instead of lines  
ps -aux | grep alex pipe result of command to grep  

#### Processes
ctrl z stop process, bg to run in background fg to run in foreground  
jobs list jobs in current shell then fg bg with job #  
\+ shows most recently placed in bg - next one stopped may we waiting for input  
  hence stopped. Run command in bg with command &  
jobs -l shows process ID  
disown removes from the job list nohup seperates process from terminal
SIGKILL (9) kill  
SIGTERM (15) request nice termination may be ignored  
SIGINT (2) request interrupt ctrl-c  
SIGHUP (1) hangup terminal closed so reload config and reopen logfiles  
SIGSTOP (19 sometimes 17 or 23) pause to be resumed later  
SIGCONT (18 sometimes 19 or 25) resume stopped process  
kill -9 or kill -SIGKILL 1467 would kill process 1467 use any signal or code  
killall -15 thing kills by name not pid  
All processes have nice value btween -20 and 19 default is 0 bigger numbers are
nicer processer which mean they aren't happy to be low priority for the CPU.
Regular user can set 0-19 once set they can't set lower nice only higher
renice -n -5 1467 would change nice value of process 1467 to -5 if you were
root user
nice +5 python thing.py & would start the command in the background and also
set the nice value
#### ps
Raw info stored in /proc for example cat /proc/uptime  
standard syntax or BSD syntax you can mix though  
ps u the u shows detailed info not the same as -u which shows for a user  
ps -f similar full in standard syntax not BSD  
##### Columns
USER who started process  
PID unique process ID  
%CPU and %MEM obvious  
VSZ virtual set size allocated memory in kb  
RSS resident set size actual amount memory being used in kb  
TTY terminal device  
STAT state of process R running S interruptible sleep + foreground Z zombie D  
uninterruptible sleep < high priority N low priority l multithreaded  
START start time  
TIME cumulative system CPU time used  
COMMAND full command and arguments  

ps ux all processes for current user  
ps aux all processes for all users  
If it's long | less  
ps -ef similar to ps aux dpending on syntax  
ps -u -u alex or ps -fu alex to show for just user alex  
ps -eo pid,user,vsz,group | less lists everything (e) the -o is followed by  
columns you want to see (pid,user,uid,group,gid,vsz,rss,comm)  
SORTING  
use --sort= can use multple comma seperated. - or + descending/ascending  
ps -eo pid,user,rss --sort=-rss | less  
ps aux --sort=-pcpu,+pmem  
ps aux --forest shows tree of parent processes  
##### Top
Load average is past 1,5 and 10 mins  
up is uptime  
P or M order py mem or processor usage  
R reverse  
f managed fields shown and order
u username to show for a user  
1 for multiple CPU  
Z set colours  
z show colours  
h help  
you can kill and renice (change priority) with k and r  


##### cgroups
Control groups  
/etc/cgconfig.conf create your own control groups  
/etc/cgrules.conf limit users or groups  
cgreate create cgroup in /sys/fs/cgroup  
blkio block input output limits io  

#### Scripts
bash scriptname  to run script file or add sha-bang #!/bin/bash to top of
source file and chmod u+x  
bash -x scriptname or set -X to display each command that is executed  
Variables untyped unless you specify with declare, if you try maths it will
convert to int on the fly if it can.
Escape with \ or put in '' to show special char literally  
NAME=value variables no spaces between =  
MY_STRING="hello"  
MY_DATE=$(date) runs date command and puts in variable  
MY_DATE=\`date\` same   
echo "$HOME \`date\`" double quotes most chars treated literally except $ ` !  
$0 is name used to invoke script $1, $2 etc are the 1st and 2nd arguments  
$# how many arguments, $@ all arguments, $? exit status of last command 0 for
OK anything else was an error  
read option NAME 1 NAME2 user input  
read -p "type first name and surname: " fn sn  
echo "hello $fn $sn"  
echo ${x:="not set"} prints x if it's set or not set if it isn't  
x="alexalexbob"  
echo {x#*alex} remove shortest match from start of variable x to *alex  
echo {x##*alex} remove longest match from start of variable x to *alex  
echo {x%*alex} remove shortest match from end of variable x to *alex  
echo {x%%*alex} remove longest match from end of variable x to *alex  
x=23  
let result=$x+5 maths  
Conditionals  
```bash
if [expression] ; then  
    do stuff here  
elif [another expression] ; then  
    do something else  
else  
    do something else  
fi
```  
Boolean expression examples  
[$NAME = "Alex"]  
[$VALUE -eq 23]  
[$NAME != "Alex"]  
[-f "myfile.py"] file exists and is regular file  
[ ! -f "myfile.py" ] not above  
DO THIS  
help test will list all the tests you can do  
[ -f "myfile.py" ] is like test -f "myfile.py"  
[ -f "$FILENAME" ] quoting allows for possible space  
[ test  ] || action will do action if test false  
[ -d "$dirname"  ] || mkdir "$dirname"  
[ test  ] && action will do action if test true so you can combine to a if then
else shorthand if $dirname exists echo else make the directory  
[ -e $dirname  ] && echo $dirname already exists || mkdir $dirname  

