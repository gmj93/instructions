# Basic commands

CTRL+A (Changed from default CTRL+B)
Called **leader** from now on

List bindings: leader ?

# List existing sessions

```
tmux ls
```

# Rename a session

```
leader $
```

# Attach/Detach/Kill session

Attach
```
tmux a -t session_name
```

Detach
```
leader d
```

Kill
```
tmux kill-session -t myname
```

# Show existing sessions / Change session

leader s

# Create window

leader c

# Move through windows

Next window:     `leader n`
Previous window: `leader p`
Rename window:   `leader ,`
Kill window:     `&` or just `exit`

# Pane

Split vertically:       leader |
Split horizontally:     leader leader -
Navigation:             leader arrows
Convert pane to window: leader !
Close pane:             leader x
Zoom (unzoon) on pane:  leader z 

## Resize Pane

```
leader :
resize-p -X Y         Where X in {U, D, L, R} and Y is a value to move
```

# Copy/Paste

Enter selection mode:

`leader + [`

Start selecting with `space`. Press `enter` to copy. Optionally press `leader + Ctrl+c` top copy to system clipboard (if enabled by config below). Pasting works in the same window with `leader + ]`. For other windows use ctrl+v, shift+insert, etc.

# Configuration file for better operation

NOTE: Needs `xsel` to be installed for the copy functionality

Edit `~/.tmux.conf` and add

```
# remap prefix from 'C-b' to 'C-a'
unbind C-b
set-option -g prefix C-a
bind-key C-a send-prefix

# Split panes using | and -
bind | split-window -h
bind - split-window -v
unbind '"'
unbind %

bind -n M-Left select-pane -L
bind -n M-Right select-pane -R
bind -n M-Up select-pane -U
bind -n M-Down select-pane -D

# Enable mouse mode (tmux 2.1 and above)
set -g mouse on

# Don't rename windows automatically
set-option -g allow-rename off

# Set vi status-keys
set -g mode-keys vi

# Enable saving copied text to system clipboard
bind C-c run "tmux save-buffer - | xsel -bi"
```

# Big tmux cheatsheet

https://gist.github.com/MohamedAlaa/2961058 
