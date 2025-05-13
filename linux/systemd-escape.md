

```sh
systemctl --user start un-eleventy@/home/frida/code/web/projects/sites/11ty/haerer-geruestbau # doesn't work
```


```sh
systemctl --user start un-eleventy@/home/frida/code/web/projects/sites/11ty/haerer-geruestbau # doesn't work
systemctl --user start $(systemd-escape --template un-eleventy@.service /home/frida/code/web/projects/sites/11ty/haerer-geruestbau) # works
systemd-escape --template un-eleventy@.service /home/frida/code/web/projects/sites/rjuschka/11ty/eleventy.cjs
```
## without escape

```ini
# myTemplate@.service
[Unit]
Description = An example of executing a script as a system service using a template unit file

[Service]
Type = oneshot
Environment = CONFIGPATH=%I
ExecStart = /home/alice/example.sh ${CONFIGPATH}

[Install]
WantedBy = default.target
```

```sh
systemctl start myTemplate@alice 
# passed alice as the only parameter to the service
# systemd replaced the identifier %I in the template unit file with alice
```

## multiple arguments

```sh
systemd-escape --template myTemplate@.service "alice bob carol"
# myTemplate@alice\x20bob\x20carol.service

systemctl start $(systemd-escape --template myTemplate@.service "alice bob carol")
# we use command substitution for passing the output of systemd-escape to systemctl
```
