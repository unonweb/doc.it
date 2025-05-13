# chmod

```sh
chmod -R
chmod --recursive # change files and directories recursively
chmod -c
chmod --changes # like verbose but report only when a change is made

chmod u # user
chmod g # group
chmod o # others
chmod a # all

chmod ug+rwx example.txt # add read, write, and execute for user and group
chmod g+wx --recursive /home/payload
chmod o+r example2.txt # add read for others
```
