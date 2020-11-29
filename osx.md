
## Table of contents

- [About](#about)


## About
Set up new Mac system.

## SSH Keys

On remote mac enable remote login in system settings sharing.Then
you can ssh with user@192.168.1.27 but using keys is safer.

On new mac

```
ssh-keygen -t rsa
```
This wil put id_rsa and id_ras.pub in ~/.ssh

Copy your public key on new mac to remote mac

```sh
cat ~/.ssh/id_rsa.pub | ssh alex@192.168.1.27 'cat>>~/.ssh/authorized_keys'
```

Now you and ssh using key and not password, if you don't want to keep typing
password for your key then add it to ssh agent

```sh
ssh-add .ssh/id_rsa
```


## Apple Mail 

Security & Privacy give Full Disk Access to iTerm and Mail
delete ~/Library/Mail and do a ln -s /Volumes/removabledisk/mailstuff ~/Library/Mail
Can't see Junk & Deleted - Small + hover and add favourites

Annoying notifications not timing out
```sh
defaults write com.apple.notificationcenterui bannerTime SECONDS
```

Install Anaconda for python environments

Install the Homebrew package manager
```sh
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Install useful packages
```
brew install ffmpeg
brew install wget
brew install htop
brew tap caskroom/cask
brew cask install google-chrome
brew cask install iterm2
brew install macvim
brew cask install iterm2
brew install youtube-dl
```

Itunes Backup to external drive

System Prefs/Security & Privacy/ Full Disk Access add iterm so we can access the directory
Move Backup folder from ~/Library/Application\ Support/MobileSync/Backup to /Volumes/T5/backup/itunes/Backup
or wherever you want it.

```
ln -s /Volumes/T5/backup/itunes/Backup ~/Library/Application\ Support/MobileSync/Backup/
```

## Crontab

* * * * * your command


```bash
env EDITOR=vim crontab -e

crontab -l

* * * * *  command to execute
│ │ │ │ │
│ │ │ │ └─── day of week (0 - 6) (0 to 6 are Sunday to Saturday, or use names; 7 is Sunday, the same as 0)
│ │ │ └──────── month (1 - 12)
│ │ └───────────── day of month (1 - 31)
│ └────────────────── hour (0 - 23)
└─────────────────────── min (0 - 59)
```
