
## Table of contents

- [About](#about)


## About
Set up new Fedora system.

Settings>Power>Automatic suspend On/Off depending on if it's running a service or just an occasional use desktop.

```sh
su

dnf upgrade
dnf install gnome-tweak-tool
dnf install gnome-shell-extensions-dash-to-dock

sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```

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



