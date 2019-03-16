
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
