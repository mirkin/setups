
## Table of contents

- [About](#about)


## About
Set up new Mac system.

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
