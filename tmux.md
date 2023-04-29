## tmux config file
copy `tmux/.tmux.conf` to `~/.tmux.conf`, press <C-a><shift>i to install plugins

### use tpm as tmux plugin manager
```bash
$ git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```
Add `set -g @plugin 'tmux-plugins/tpm'` to `~/.tmux.conf`
