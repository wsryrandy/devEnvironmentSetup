# devEnvironmentSetup
This is a development environment setup for golang, c++ and python using zsh, tmux and nvim... 

## font 
Before all, install one of the Nerd Fonts from `https://www.nerdfonts.com/font-downloads`, current selection is `JetBrainsMono`.

## zsh setup
### zsh
Install zsh
```bash
sudo apt install zsh
```

Install oh-my-zsh
```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

In `~/.zshrc` setup DEFAULT_USER=$username to avoid all unnecessary directories in the command prompt 

### powerlevel10k
clone Powerlevel10k repo
```bash
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

Set `ZSH_THEME="powerlevel10k/powerlevel10k"` in `~/.zshrc`

Follow instructions to setup powerlevel10k theme

### plugins
#### zsh-autosuggestions
```
https://github.com/zsh-users/zsh-autosuggestions/blob/master/INSTALL.md
```

#### zsh-syntax-highlighting
```
https://github.com/zsh-users/zsh-syntax-highlighting/blob/master/INSTALL.md
```

#### web-search
Add web-search into plugins in ~/.zshrc


