# Tmux Cheatsheets

## Sessions

```bash
# start a new session
tmux
tmux new
tmux new-session
:new

# start a new session or attach to an existing session named mysession
tmux new-session -A -s mysession

# start a new session with the name mysession
tmux new -s mysession
:new -s mysession

# delete session mysession
tmux kill-ses -t mysession
tmux kill-session -t mysession

# delete all sessions but the current
tmux kill-session -a

# delete all sessions but mysession
tmux kill-session -a -t mysession

# rename session
Ctrl+b $

# Detach from session
Ctrl+b d

# show all session
tmux ls
tmux list-sessions

# attach to last session
tmux a
tmux at
tmux attach
tmux attach-session

# attach to a session with the name mysession
tmux a -t mysession
tmux at -t mysession
tmux attach -t mysession
tmux attach-session -t mysession

# session and window preview
Ctrl+b w

# move to previous session
Ctrl+b (

# move to next session
Ctrl+b )

```

## Windows

```bash
# start a new session with the name mysession and windows mywindow
tmux new -s mysession -n mywindow

# create window
Ctrl+b c

# rename current window
Ctrl+b ,

# close current window
Ctrl+b &

# list windows
Ctrl+b w

# previous window
Ctrl+b p

# next window
Ctrl+b n

# switch window by number
Ctrl+b 0..9

# toggle last active window
Ctrl+b l

# reorder window, swap window number 2 and 1
:swap-window -s 2 -t 1

# move current window to the left by one position
:swap-window -t -1

# move current window to the right by one position
:swap-window -t +1

# move window from source to target
:move-window -s src_session:win -t target_session:win
:movew -s foo:0 -t bar:9
:movew -s 0:0 -t 1:9

# repopition window in the current session
:move-window -s src_session:src_window
:movew -s 0:9

# renumber windows to remove gap in the sequence
:move-window -right
:movew -r

```

## Panes

```bash
# toggle last active panes
Ctrl+b ;

# split the current pane with a vertical line to create a horizontal layout
:split-window -h
Ctrl+b %

# split the current with a horizontal line to create a vertical layout
:split-window -v
Ctrl+b "

# join two windows as panes(merge window 2 to window 1 as panes)
:join-pane -s 2 -t 1

# move pane from one window to another(move pane 1 fron window 2 to pane after 0 of window 1)
:join-pane -s 2.1 -t 1.0

# move the current pane left
Ctrl+b {

# move the current pane right
Ctrl+b {

# swith to pane to the direction
Ctrl+b up
Ctrl+b down
Ctrl+b left
Ctrl+b right

# toggle synchronize-panes(send command to all panes)
:setw synchronize-panes

# toggle between pane layouts
Ctrl+b Spacebar

# switch to next pane
Ctrl+b o

# show pane numbers
Ctrl+b q

# switch pane by number
Ctrl+b q 0..9

# toggle pane zoom
Ctrl+b z

# convert pane into a window
Ctrl+b !

# resize current pane height
Ctrl+b up
Ctrl+b Ctrl+up
Ctrl+b down
Ctrl+b Ctrl+down

# resize current pane weight
Ctrl+b left
Ctrl+b Ctrl+left
Ctrl+b right
Ctrl+b Ctrl+right

# close current pane
Ctrl+b x

```

## Copy Mode

```bash
# use vi keys in buffer
:setw -g mode-keys vi

# enter copy mode
Ctrl+b [

# start selection
Spcaebar

# copy selection
Enter

# clear selection
Esc

# paste contents of buffer_0
Ctrl+b ]

# display buffer_0 contents
:show-buffer

# copy entire visible contents of pane to a buffer
:capture-pane

# show all buffers
:list-buffers

# show all buffers and paste selected
:choose-buffer

# save-buffer buf.txt
:save-buffer buf.txt

# delete buffer_1
:delete-buffer -b 1

```

## Misc

```bash
# enter command mode
Ctrl+b :

# set OPTION for all sessions
:set -g OPTION

# set OPTION for all windows
:set OPTION for all windows

# enable mouse mode
:set mouse on

```

## Help

```bash
# list key bindings
tmux list-keys
:list-keys

# show every session, window, pane, etc...
tmux info

```