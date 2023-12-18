tpm - tmux plugin manager
Сначала нужно создать конфигурацию: ~/.tmux.conf
Туда прописываем:
```
#Plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'

#Run plugin manager
run '~/.tmux/plugins/tpm/tpm'
```

After that restart tmux.



bind s chose-tree sZ -O name - сщртировка сессий по алфавиту, а не по времени создания
setw -g pane-base-index 1 - окна номируются с 1, а не с 0

set -g mouse on - включить поддержку мыши

set-window-option -g mode-keys vi - поддержка Vim-овских клавиш

bind-key -T copy-mode-vi 'v' send -X begin-selection
bind-key -T copy-mode-vi 'y' send -X copy-selection


## Плагины
Сначала прописываем все плагины в конфиге, потом
Ctrl+B Shift+i - установить все плагины

set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
set -g @plugin 'christoomey/vim-tmux-navigator'
set -g @plugin 'jimeh/tmux-themepack'




`

