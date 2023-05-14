## nvim config
### enable copy-paste from system clipboard
Add `opt.clipboard="unnamedplus"` into nvim config file(`~/.config/nvim/lua/custom/init.lua`), make sure `xclip` is on the path (`sudo apt intall xclip`)

# the CHAD NVIM 
## load nvchad
This is a much easier way to setup nvim, but it needs a newer version of nvim from snap, current wsl does not support it well

```
sudo snap install nvim --classic
```
```
alias vim=nvim
echo 'alias vim=nvim' >> ~/.zshrc
```
Need to install one of the Nerd font, currently use JetBrains Mono.

use 
```bash
$ git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
```
for mac and Linux and use 
```bash
$ git clone https://github.com/NvChad/NvChad $HOME\AppData\Local\nvim --depth 1
```
for Windows

May need to install gcc as needed:
```bash
$ sudo apt update
$ sudo apt-get install build-essential
```
## theme
Inside nvchad, type `space + t + h`, and you can choose catppuccin

## Syntax highlighting
`:TSInstallInfo` to find out if the syntax highlighting is available

`:TSInstall cpp` to install new ones

## File tree
`ctrl + n` to open/close file tree

use `m` to mark a file

use `a` to add a new file

use `c` to copy file

use `p` to paste file

use `r` to rename file

## file navigation
use `space + f + f` to find file in all files

`space` is the leader key

use `space + f + b` to find file in opened buffers

## cheatsheet
use `space + c + h` to open and close it

## window navigation
`:vsp` to vertically split and `:sp` to horizontally split
and use `ctrl + h/j/k/l` to navigate through the windows

## tabb/buffers
`tab` to go to next buffer
`shift + tab` to go to previous buffer
`space + x` to close the current buffer

## terminal
can open terminal in a horizontal way `space + h`
can open terminal in a vertical way `space + v`

actually, it is better to use `alt + h/v` to open or hide a terminal
and `alt + i` will open or hide a floating terminal

## org mode
add following lines in cu
