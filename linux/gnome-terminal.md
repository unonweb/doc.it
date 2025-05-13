# gnome-terminal

```sh
gnome-terminal --tab --tab # The tabs appear in the last created gnome-terminal
gnome-terminal -- sleep 10s # run sleep inside a newly created terminal
gnome-terminal -- bash -c "sleep 2s; echo foo; exec bash -i"
# '-c' run a sequence of commands in a new shell
# 'exec bash -i' open an interactive shell (as a result, the terminal stays open)
```
