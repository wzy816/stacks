# tmux

<PREFIX> = &lt;Ctrl+b> by default, my preference is <Ctrl-a>

```bash
# install
brew install tmux

# check version
tmux -V
```

## session

```bash
# new session
tmux new

# new session with names
tmux new -s <session_name>

# exit session
exit

# show session list
tmux ls
tmux list-sessions

# attach latest session
tmux a #
tmux attach-session

# attach specific session
tmux a -t <session zero-based id>
tmux a -t <session_name>

# kill session
tmux kill-session -t <session_name>
```

## window

```bash
# create new window
<PREFIX> c

# close window
<PREFIX> &

# rename window
<PREFIX> ,

# previous window
<PREFIX> p

# next window
<PREFIX> n
```

## panels

```text
# split horizontal
<PREFIX> "

# split vertical
<PREFIX> %

# convert to a window
<PREFIX> !

# close pane
<PREFIX> x

# copy
<PREFIX> [

# paste
<PREFIX> ]

# enable paste to clipboard
# in iTerm2 Preference > General, click
# applications in terminal may access clipboard
```

## tmux-resurrect
```
git clone https://github.com/tmux-plugins/tmux-resurrect ~/.tmux/tmux-resurrect/
```

## .tmux.conf

```conf

# remap prefix from 'C-b' to 'C-a'
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# split panes using | and -
bind | split-window -h
bind _ split-window -v
unbind '"'
unbind %

# Set new panes to open in current directory
bind c new-window -c "#{pane_current_path}"
bind '"' split-window -c "#{pane_current_path}"
bind % split-window -h -c "#{pane_current_path}"

# reload config file (change file location to your the tmux.conf you want to use)
bind r source-file ~/.tmux.conf \; display-message "Config reloaded..."

# Enable mouse mode (tmux 2.1 and above)
# Mouse-mode has been rewritten.  There's now no longer options for:
# - mouse-resize-pane
# - mouse-select-pane
# - mouse-select-window
# - mode-mouse
# Instead there is just one option:  'mouse' which turns on mouse support
set -g mouse on

set-option -g history-limit 300000

# Refresh status line every 5 seconds
set -g status-interval 5

#Set the left and right status
set -g status-left '#[bg=colour7]#[fg=colour0]#{?client_prefix,#[bg=colour2],} #S #[bg=colour7]#[fg=colour7]#{?client_prefix,#[fg=colour2],}#{?window_zoomed_flag, 🔍 ,}#[fg=colour1]'
set -g status-right '#[bg=colour7]#[fg=colour0] %a %d-%m-%Y %H:%M:%S '

# Set the background color
set -g status-bg colour8

# customize how windows are displayed in the status line
set -g window-status-current-format "#[fg=colour8]#[bg=colour4]#[fg=colour7]#[bg=colour4] #I* #[fg=colour7] #W #[fg=colour4]#[bg=colour7]"
set -g window-status-format "#[fg=colour244]#[bg=colour8]#I-#[fg=colour240] #W"

# Automatically set window title
set-window-option -g automatic-rename on
set-option -g set-titles on

run-shell ~/.tmux/tmux-resurrect/resurrect.tmux
# prefix + Ctrl-s - save
# prefix + Ctrl-r - restore

```
