
## About
I'm running various Linux distros other than my main Fedora machine along with OSX. I use a variety of frameworks, environments, and languages. I'll often switch back to an OS, Editor, Language combination I've not used for a while and I'm all fingers and thumbs forgetting how to do the simplest of tasks.

This is a repo if set up instructions for new installs along with reminders on how to do various tasks and use common programs and systems.


## Table of contents

- [About](#about)
- Operating Sytems
  - [Fedora](fedora.md)
  - [Raspberry Pi](rpi.md)
  - [OSX](osx.md)
- Environments
  - [Anaconda](anaconda.md)
-Software
  - [Vim/Vi](vim.md)
  - [Figlet](#figlet)
- Development
  - [iOS/XCode/Swift](iosdev.md)


## VSCode

### Remote Development

Make sure ssh keys are on remote ~/.ssh/authorized_keys and download VSCode Remode Development extension pack
Then in VSCode Remote-SSH:Add New Host and Remote-SS:Connect To Host I selected bottom left green with arrows.

It will ask for a path so be quite specific you don't want to include unecessary folders in this case live watch
wasn't able to monitor so many folders so I had to do the following.

edit user settings in VSCode then restart VSCode
```
"files.watcherExclude": {
    "**/.git/objects/**": true,
    "**/.git/subtree-cache/**": true,
    "**/node_modules/*/**": true
  }
```
  
## Figlet
figlet for ascii art  
```sh
figlet mytext  
showfigfonts  
sudo wget http://www.figlet.org/fonts/larry3d.flf -O /usr/share/figlet/larry3d.flf  
figlet -f larry3d alexander  

        ___                                      __                    
       /\_ \                                    /\ \                   
   __  \//\ \      __   __  _    __      ___    \_\ \     __   _ __    
 /'__`\  \ \ \   /'__`\/\ \/'\ /'__`\  /' _ `\  /'_` \  /'__`\/\`'__\  
/\ \L\.\_ \_\ \_/\  __/\/>  <//\ \L\.\_/\ \/\ \/\ \L\ \/\  __/\ \ \/   
\ \__/.\_\/\____\ \____\/\_/\_\ \__/.\_\ \_\ \_\ \___,_\ \____\\ \_\   
 \/__/\/_/\/____/\/____/\//\/_/\/__/\/_/\/_/\/_/\/__,_ /\/____/ \/_/   
                                                                       
```

## VLC
Mac play loop write down the seconds for A and B
Press cmd-J to jump to the start of your loop, then cmd-shift-L to mark it. e.g. 4:34
Press cmd-J to jump to the end of your loop, then cmd-shift-L to mark it.

You can then watch your loop.

Press cmd-shift-L a third time to cancel the loop.

## Youtube-dl

Download audio

```bash
youtube-dl -x --audio-format mp3 https://www.youtube.com/watch?v=hc0eNdAAmqY
```

Snip just a part, first get the url eith -g then use ffmpeg

```bash
ffmpeg -ss 00:02:46.00 -i $(youtube-dl -g -x --audio-format mp3 https://www.youtube.com/watch?v=hc0eNdAAmqY) -t 00:00:42.00 mySong.mp3
```

## rsync

Copy and sync files, often use this to keep projects on NAS but work on fast local drive and push back changes.

-a is like -r recursive but keeps symbolic links, dates, owners, etc. P to show progress and resume. 
-E preserve executability
--delete is dangerous but we want to remove files in the destination that no longer exist in the source folder.

```bash
rsync -aPE --delete /Volumes/H/projects/2019/ /Volumes/T5/dev/projects/2019
```
b and --backup-dir will backup the deleted files which could be safer.

```bash
rsync -abPE --delete --backup-dir="backup $(date +\%y-\%m-\%d)" /Volumes/H/projects/2019/ /Volumes/T5/dev/projects/2019
```

## Common Shell Tips

| Task | Shortcut/Command |
| --- | --- |
| Move cursor to beginning of line | `Ctrl + A` (Emacs mode), `0` or `Shift + 6` (vi mode) |
| Move cursor to end of line | `Ctrl + E` (Emacs mode), `$` or `Shift + 4` (vi mode) |
| Save a file in vi when you forget to use `sudo` | `Esc`, `:w !sudo tee %`, enter password, `Enter` |
| Start `ssh-agent` and add your SSH key | `eval "$(ssh-agent -s)"`, `ssh-add /path/to/your/key` |

