
## Table of contents

- [About](#about)
- [SSH Keys](#setup-ssh-keys)
- [Dot files](#dot-files)


## About
Set up new Fedora system.

Settings>Power>Automatic suspend On/Off depending on if it's running a service or just an occasional use desktop.

```sh
su

dnf upgrade

hostnamectl set-hostname fed1

dnf install gnome-tweak-tool gnome-shell-extensions-dash-to-dock
dnf install ffmpeg vlc inkscape audacity youtube-dl autojump vim-enhanced figlet

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



