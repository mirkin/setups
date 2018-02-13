## About
There are times in my Vi ninja mode, I'm in an editor and wish it had Vi mode. Although spend too much time in another IDE or editor and I get rusty. These are my Vi notes.

## Table of contents

- [About](#about)
- [The basics](#basics)


## VIM

RasPi had to change version to get powerline working  

```sh
sudo apt-get install vim-nox  
```

Buffer = in-memory text can have many  
Window = viewport of buffer can tile and split  
Tab = Collection of windows  

config file is ~/.vimrc  
syntax on " syntax hilite  
set number " show line numbers  
filetype indent on " filetype specifix indents good for coding  
set wildmenu " shows autocomplete matches with tab in command mode  
set modeline " allows you to do make settings in a file using # vim: tabstop=8
so your python or javascript file can send settings to vim which only apply for
that file. 

## Basics
hjkl to move cursor  
ESC  or ctrl-\[ normal mode  
i insert  
a append  
R replace mode type over the top  
A append at end of line  
:q! quit don't save  
ZZ or :wq save and quit  
u undo, ctrl r redo, U undo entire line  
p inserts text just deleted after cursor  
r replace char with next char to be typed, c change so ce then type will change
to the end of a word c$ change to end of line  
ctrl g - line number and col etc.

**USEFUL**  
J - Join current line with next line, brings next line up and appends  

SHELL STUFF  
:! followed by any shell command to execute a command  
:r !figlet alex runs a shell command and insters under current line  
ctrl-z suspends vim so you can do work in your shell fg resumes vim  
:sh starts new subshell and ctrl-d or exit to get back to vim  

% will find matchin parentheses

Navigating  
gg start, G end, lineNumber then G go to line  
G201 go to line 201  
crtl b page up ctry f page down  
w move in words W move in WORDS b B  
0 start of line $ end of line  
A append at end of line  
^ first non whitespace char useful for coding  
I same as above but switch to insert mode  
`` previous cursor position  
'' previous line  
f{char} find (move cursor to next occurance of char on that line ; will 
repeat the last search and , will repeat in reverse) F{char} searches backward
and t{char} T{char} are like f but stop once char early (to)  
;,  
3fb find 3rd b on line from cursor  
F and T do the same but backwards  

Insert Normal Mode  
ctrl-o - from insert mode to normal mode for one command and then it switches
back so you don't have to. So you can be editing and if you want to have the
line in the middle of the screen (zz) you don't have to press escape zz i you
just do ctrl-o zz and continue.


Insert Mode  
ctrl-h backspace  
ctrl-w delete back word  
ctrl-u delete back to start of line  

Search  
/phrase  
?phrase searches backward  
search again n opposite dir N  
\* go to next occurance of word currently under cursor  
\# as above but backward  
g* go to next even if it's part of another word eg. set settings  
g#  
n next result forward  
N backward  


Replace  
:s/old/new  
:s/old/new/g all on line  
:%s/old/new/g whole file  
:%s/old/new/gc whole file but with prompts  

Like mouse selecting and typing over:  
cW change till end of word  
3cW change 3 words  
BcW change from beginning to end of word   
ci" change innder text beween "" can use (<etc.  
cit change inner tag <></>  
yiw yank inner word copy current word even if cursor not at start  
viwp visually select inner word and paste over with what was coppied  
R replace, just start typing over everything  
A append at end  
I insert at start like ^ but insert mode  
s delete char at cursor and enter insert mode  
S clear line and being insert  
C delete from cursor to end and begin insert mode  
o new line after  
O new line before  

Marking  
ma mb ... mz set marker in named register a-z  
'a 'z goto start of line of marker  
`a `z goto marker  
:marks show all marks  

MAtching Quotes
ci (change inner quote) then ' or " or { or <

Autocomplete
ctrl-n
ctrl-p suggest match from start of current word ctrl-n and ctrl-p to select menu

Replace Mode [type over]  
R  
gR treats tabs as spaces  

## Copy Paste  
v for visual mode char wise  
V for full lines  move cursor y to yank (copy), d to cut, c to change  
ctrl-v visual-block  
gv reselect last visual selection  
<ctrl-g> in visual mode switches to select which is like regular text editor
selections  
p paste after cursor  
P paste before  
copy and paste from buffers "ay copy to a buffer "bP paste from b buffer  
gv get back to previous visual selection  
yy yank line  
y yank 2yW copy 2 WORDS  
:364y yank line 364  
:364,400y yank line 364 to 400  
:364,400t500 yank lines and paste under line 500  
:364,400t yank lines and paste under current line   
MOVE  
:364m 400 move line 364 to after 400  
:364,370m 400  
yt{char} yank till before {char} to right  
yT{char} yank till after {char} to left  
yf{char} yank forward to char to right  
yF{char} yank yank left to char  
Registers  
"ay yank to register a  
"Ay append to register a  
"ap put from register a  
"+p paste from clipboard linux  
"*p pase from clipboard windows or mouse hilight linux"  
"ayy yank line to reg a  
ctrl-r{register} paste from register in insert mode. Saves switching 0 is
default register.  
ctrl-r ctrl-p {register} same but fixes indentation  
:reg list registers  
:let @a=system('ls -al') puts result of ls -al into reg a  
ctrl R a  
Paste/Put not lining up correctly? Probably pasting into the terminal so when
it pastes new lines it's formatting it like you are typing it in. You need to
tell it to stop trying to format your code.  
set paste  
set nopaste to turn it back on.  
Perhaps .vimrc set pastetoggle=<F2> or something if you are doing it often  

*while in visual block mode *  
motions work to change block eg 20l or 10j  
o changes corner that defines the block VERY USEFUL  
I,A - insert at top when done it will be repeated on each line  
d delte block  
U uppercase  
u lower  
~ toggle case  
r replace all chars in block with next char pressed  
. repeat  
: command mode will automatically preffix command with just selected text  
:'<,'>  
Markers are  
'< start line  
'> end line  
\`< start character  
`> end character  
So to replace red with green for current selection  
:'<,'>s/red/green/g  
gv - reselect last visual selection  
vit - select inner tag  


Save & Quit  
ZZ or :wq  
Quit no save  
ZQ or :q!  

OPEN  
:E file browser  

Indent  
5>> indent 5 lines  
Vjjjjj> to do it visually  

Delete  
x del char or 8x delete 8  
dw delete from cursor to start of next word or d8w  
de delete frtom cursor to end of current word e8w  
d$ delete from cursor to end of line  
dd delete line 8dd delete 8 lines  
daw delete a word  
diw delete inner word  
dap delete a paragraph  

Motion - move to (AS Above can add number before to repeat X times)  
w,e,$(end of line),0(start of line),^ first non whitespace on line  
w move word  
W move WORD  
b back word  
B back WORD  
e forward to end of next word  
E forward to end of next WORD  
f(char) forward to next char [on same line]  
F(char) back to prev char  
t and T as above but just before char  
60i#<esc> insert 60 # signs  


Scrolling  
zz current line to middle of screen COOL  
zt top  
zb bottom  
ctrl-e ctrl-y step up/down a line  
ctrl-d ctrl-u 1/2 page  
ctrl-b ctrl-f page  
ctrl-o switches from insert to normal temporarlily while you do one of these
commands.  

Buffers  
:ls list  
:bn next buffer  
:bp prev  
:b2 buffer 2  
:b filen    partial filename tab will auto complete  

Windows  
:split or :sp filename horizontal split ctrl-w s  
:vsplit or :vs or :vsp filename vertical split ctrl-w v  
ctrl-w arrows move cursor to window or ctrl w again to cycle  
For horizontal splits  
ctrl-w + - resize _ maximize window = all equal size  
10 ctrl-w + increase size by 10
For vetical  
ctrl-w < or > resize | maximize = equal  
10 ctrl-w > or < change size by 10
:hide close current window ctrl-w q  
:only close all but this  
10 ctrl-w + increase size by 10 - decrease  
:qa quit all  
:wqa :xa save all quit  
ctrl-w x exchange window with neigbour  

Tabs should think of tab as a window layout  
Tabs vim -p file 1 file2 file3  
gt gT     normal mode switch t  ab
2gt numbered tab  
:tabn :tabp prev next tab  
:tabedit filename  
:tabclose  

Folding  
set foldmethod=syntax or manual indent  
zM fold everything  
zR unfold everything  
za toggle open/close a fold  
zm fold more  
zr fold less  

Plugins crtlP fuzzy search
cd ~/.vim
git clone https://github.com/kien/ctrlp.vim.git bundle/ctrlp.vim
add to .vimrc set runtimepath^=~/.vim/bundle/ctrlp.vim
:helptags ~/.vim/bundle/ctrlp.vim/doc

find out where it's looking for scripts/plugins etc
:set runtimepath? 
add to .vimrc to add to that path
set runtimepath^=~/.vim/bundle/ctrlp.vim

Plug 'Raimondi/delimitMate'
sorts matching pairs ("")etc
shift tab to skip to end of delimeter
ctrl-g g to skip contiguous.
" enable delimit mate plugin to expand spaces and cr
 73 let delimitMate_expand_space=1
 74 let delimitMate_expand_cr=1
 
Download theme via shell (for example badwolf)
```
wget https://raw.githubusercontent.com/sjl/badwolf/master/colors/badwolf.vim -O .vim/colors/badwolf.vim
```


## Sessions 
Save
:mksession or :mks ~/.vim/sessions/mysession.vim
Open
:source ~/.vim/session.mysession.vim
Or
vim -S ~/.vim/session.mysession.vim
Overwrite session 
:mks!

## Recording
qa to start recording to register a qz to record to register z
q in normal mode will stop
@a will play from register a

## Case  
g~ swap case  
gu lowercase  
gU uppercase  
gUaw current word upper  
gUap current paragraph upper  
gUit go uppercase in tag <></>  

## Maths
ctrl a and ctrl x will increase or decrease a number
you don't need to be on the number it will find the next one on that line
20<ctrl-a> add 20
10<ctrl-x> subtract 10
Even works wth bases 0xef for example
<ctrl-r>=2*15<CR> while in insert mode puts result of maths expression at cursor

Running shell commands on current buffer
:%!js-beautify -f - --type html
:%!js-beautify -f - --type js -w 80

## Spelling
:set spell turn it on
[s to prev error
]s to next error
z= suggestions for current word
zg add current word to dictionary
zw remove current word from dic
:set spellang=en_gb set dictionary
<ctrl-x>s spelling suggestions from insert mode

## Special Chars
<ctrl-v>065 while in insert mode would place an A at cursor
<ctrl-v>u00bf will put the unicode char un
<ctrl-k>12 insert char by digraph 12 being a half
ga will show unicode value of current char at bottom of screen
:digraph will show massive list of chars

## CTAGS
JS
npm install -g git+https://github.com/ramitos/jsctags.git
jsctags piano-local.js -f | sort > tags
Found this seems to work better with closures
npm install -g javascript-ctags
Exuberant version you can edit ~/.ctags which is in my github .dotfiles
.vimrc set tags+=tags;$HOME so vim will search for tags file backwards up the tree
:tago <ctrl-d> see tags
b]
<ctrl-]> from tag to definition
<ctrl-t> navigate back through tag list history
:tnext
:tfirst
:tlast
:tprev
:tag{keyword} with tab autocomplete
:tjump{keyword}
