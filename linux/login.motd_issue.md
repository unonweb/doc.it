
- The shell displays these messages only on CLI. Users will not see these messages before and after a graphical login.
- The **/etc/issue** file is typically used to **display a login message** or banner.
- The **/etc/motd** file is generally used to **display an issue, security policy**, or message.
- These messages are global. These messages will be displayed to all users at the CLI prompt.

# FILES

```sh
# display messages
sudo nano /etc/issue #  before login
sudo nano /etc/motd #  after login
```