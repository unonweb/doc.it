# tee

`tee` reads from the standard input and **writes to both standard output and one or more files at the same time**.

```sh
echo -e "content" | sudo tee ./test.conf # writes "content" into the new file test.conf
echo -e "content" | sudo tee -a /etc/hosts # appends "content" to the file /etc/hosts
```

**Redirect stderr to `tee` while keeping it going to the terminal:**

```sh
command 2> >(tee error.log >&2)
```

- `command`: This is the command you are running.
- `2>`: Redirects stderr (file descriptor 2).
- `>(...)`: This syntax creates a process substitution. It allows you to redirect output to a command.
- `tee error.log`: This command reads from stdin and writes to both stdout and the specified file (`error.log`).
- `>&2`: Redirects the output of `tee` back to stderr.
