
## Table of contents

- [About](#about)
- [SSH Keys](#setup-ssh-keys)
- [Dot files](#dot-files)
- [Desktop Aesthetics](#desktop-aesthetics)
- [SAMBA](#samba)
- [Apple Keyboard](#apple-keyboard)


## About
Set up new Fedora system.

Settings>Power>Automatic suspend On/Off depending on if it's running a service or just an occasional use desktop.

```sh
su

dnf upgrade

hostnamectl set-hostname fed1

dnf install gnome-tweak-tool gnome-shell-extensions-dash-to-dock
dnf install ffmpeg vlc inkscape audacity youtube-dl autojump vim-enhanced figlet unzip gimp

dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

Enable Google Yum repo
```sh
cat << EOF > /etc/yum.repos.d/google-chrome.repo
[google-chrome]
name=google-chrome - \$basearch
baseurl=http://dl.google.com/linux/chrome/rpm/stable/\$basearch
enabled=1
gpgcheck=1
gpgkey=https://dl-ssl.google.com/linux/linux_signing_key.pub
EOF
```
How does it work? heredoc for the shell
```sh
<< ANYWORD
type stuff here
multi line whatver
ANYWORD
```
to get multiline string and redirect to a new file

Install Chrome
```sh
dnf install google-chrome-stable
```

## Install Fonts
```sh
cp /newfontsfolder /usr/share/fonts
wget https://github.com/Lokaltog/powerline/raw/develop/font/PowerlineSymbols.otf -O /usr/share/fonts/PowerlineSymbols.otf
wget https://github.com/powerline/fonts/raw/master/SourceCodePro/Source%20Code%20Pro%20for%20Powerline.otf -O /usr/share/fonts/SourceCodePro.otf
fc-cache -f -v
```

Close and restart terminal>Edit>Profile Preferences>Custom font>Source Code Pro for Powerline

## Desktop Aesthetics

Tweaks>Extensions>Dash to dock>ON
Tweaks>Extensions>User themes>ON
Tweaks>Top Bar>Clock>Date>ON
Tweaks>Windows>Titlebar buttons> Max on Min on Left
Tweaks>Extensions>Dash to dock>Settings>Launchers>Mode app button beginning of dock
Tweaks>Extensions>Dash to dock>Settings>Appearance>Use built-in theme

Gnome-OSX Theme
https://www.gnome-look.org/p/1171688/

Had to take shell themes out of their parent folder.

Download and make .themes folder in your home dir
Tweaks>Appearance select your theme

Install icon theme 
```sh
sudo dnf copr enable tcg/themes
sudo dnf install la-capitaine-icon-theme
```
Or create ~/.icons go find a [theme](https://github.com/keeferrourke/la-capitaine-icon-theme) and stick it in there.

## Setup SSH keys

Generate SSH keys and use for remote SSH and Github

[Follow this link for full details](rpi.md#ssh-keys)

## Dot Files
```sh
git clone git@github.com:mirkin/dotfiles.git
ln -s dotfiles/.vimrc .vimrc
mkdir .vim/colors
wget https://raw.githubusercontent.com/sjl/badwolf/master/colors/badwolf.vim -O .vim/colors/badwolf.vim
```

Open vim and :PlugUpgrade

## Samba
What may frustrate you is SELinux turn it off and on
```sh
setenforce 0
setenforce 1
```
Better to do this though to any folders you want to share
```sh
sudo chcon -R -t samba_share_t /<path to your folder>
```
Have an NTFS partition?
```sh
sudo setsebool -P samba_share_fusefs on
```

Install and enable samba and netbios
```sh
sudo dnf sintall -y samba
sudo systemctl enable smb nmb
```
Punch a hole in the firewall
```sh
sudo firewall-cmd --add-service=samba --permanent
sudo firewall-cmd --reload
```
Add user (account must exist on that machine)
```sh
sudo pdbedit -a <username>
```
Config is /etc/samba/smb.conf need to restart after changes
```sh
sudo systemctl restart smb
```

Example add a share
```sh
[Share Name]
  path = \<folder path>
  writeable = yes
  browseable = yes
  guest ok = no
```

## Niggles
### Disk not mounting or read only?
Possibly an old disk from raid array needs raid flags removing from it.
```sh
dmraid -r -E /dev/<device sdb1 or whatever>
```

## Apple Keyboard
Pipe | key won't work so usually top left under esc ±§
Need to sort the driver modinfo hid_apple
Example temporarily get the function keys working as function keys by appending 2 to fnmode
```sh
sudo echo "2" | sudo tee /sys/module/hid_apple/parameters/fnmode
```
ctrl-alt f3 then ctrl-alt f2 to test
This won't survive a restart so add to conf file in modeprobe to pass to driver but it seems the driver is loaded very early hence mkinitcpio
```sh
echo "options hid_apple fnmode=2" | sudo tee /etc/modprobe.d/hid_apple.conf
echo 'FILES="$FILES:/etc/modprobe.d/hid_apple.conf"' | sudo tee -a /etc/mkinitcpio.conf
```
