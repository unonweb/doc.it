# LINKS

- https://man.openbsd.org/ssh_config
# SETUP

```sh
# install
sudo apt install ssh
## server
sudo systemctl enable --now ssh.service
```

```sh
## client

# check if key exists
ll ~/.ssh

# create key pair
ssh-keygen -t rsa -b 4096 -a 100 # older keytype
ssh-keygen -t ed25519 -a 100 # newer keytype

# copy key pair to machine
ssh-copy-id -p 15108 frida@lem # creates 'authorized_keys'
```

```sh
## server
# improve sshd config
sudo xed /etc/ssh/sshd_config # 
```

# CLIENT

## connect

```sh
ssh -p <port> <user>@<host> # Port to connect to on the remote host
ssh <host> # the same username on the remote machine is assumed

ssh -i <file> # selects a file from which the identity (private key) for public key authentication is read. 
# Default: 
# ~/.ssh/id_dsa
# ~/.ssh/id_ecdsa
# ~/.ssh/id_ed25519
# ~/.ssh/id_rsa
```

```sh
cat ~/.ssh/id_rsa # private key
cat ~/.ssh/id_rsa.pub # public key
cat ~/.ssh/authorized_keys
# contains the *public keys* of those clients who have permanent access to the given user account
# The client uses the private key when connecting not the public key!

cat ~/.ssh/known_hosts
```

## config

```sh
man ssh_config
```

1. options specified from the command line.
2. options defined in the `~/.ssh/config`
3. options defined in the `/etc/ssh/ssh_config`

## tunnel

```bash
ssh -p <port> -nNT -L 4000:localhost:3389 admin@<gateway>
```

## exec cmd

```sh
ssh USER@HOST 'COMMAND'
ssh USER@HOST 'COMMAND1; COMMAND2; COMMAND3'
ssh USER@HOST 'COMMAND1 | COMMAND2 | COMMAND3'
ssh USER@HOST << EOF
  COMMAND1
  COMMAND2
  COMMAND3
EOF
```

### quotation

```bash
REMOTE_PATH=udo@fk-mobil25:/home/udo/it/ansible/frida

# does not work:
ssh udo@fk-mobil25 '/usr/bin/ls -la --color=auto ${REMOTE_PATH#*:}' | less

# works:
ssh udo@fk-mobil25 "/usr/bin/ls -la --color=auto ${REMOTE_PATH#*:}" | less

```
## sudo cmd

```sh
ssh -t user@server "sudo script"
# -t Force pseudo-terminal allocation. This can be used to execute arbitrary screen-based programs on a remote machine

cat /home/user/file | ssh -t user@server "sudo tee -a /etc/dir/file"
```

## ssh-keygen

authentication key generation, management and conversion

```sh
# create key
ssh-keygen # creates a key pair interactively
# default location: /home/user/.ssh/id_rsa
ssh-keygen -t rsa -b 4096 -a 100 # older keytype
ssh-keygen -t ed25519 -a 100 # newer keytype
ssh-keygen -C <comment> # add comment
```

### change / add passphrase

If someone gains access to your computer, the attacker can gain access  to every system that uses that key. To add an extra layer of security,  you can add a passphrase to your SSH key

```sh
ssh-keygen -p -f ~/.ssh/id_ed25519 # change passphrase
```

### check fingerprint

fingerprint: is a short (hashed) version of the server's public key normally stored under */etc/ssh/*

```sh
ssh-keygen -E md5 -lf /etc/ssh/ssh_host_ed25519_key.pub # show md5-fingerprint
ssh-keygen -E SHA256 -lf /etc/ssh/ssh_host_ed25519_key.pub # show SHA256-fingerprint
```

```sh
ll /etc/ssh/*.pub # Schlüsselart: welche ist gefragt (rsa, ed25519, ecdsa)?  
ssh-keygen -E <algorithm> -lf <keypath>
ssh-keygen -E md5 -lf <keypath> # ermittelt den md5-fingerprint aus dem angegebenen Schlüsssel

ssh-keygen -l # shows fingerprint of specified public key file
ssh-keygen -f <file> # specifies the filename of the key file
ssh-keygen -E <algorithm> # specifies the hash algorithm used when displaying key fingerprints 
```

## ssh-copy-id

uploads the user's public key to the server saving it in `~/.ssh/authorized_keys`

* `~/.ssh/authorized_keys` list of clients that may log into this ssh server

```sh
ssh-copy-id user@host
ssh-copy-id -i <pubKey> # use only the key(s) contained in file
```

```sh
# without ssh-copy-id:

# client
cat ~/.ssh/id_rsa.pub # show keyfile --> copy the content

# server
ssh <user>@<server> # connect to the server via ssh with the user you would like to add your key to
mkdir -p ~/.ssh && touch ~/.ssh/authorized_keys # create the ~/.ssh directory and the authorized_keys file
```

# SERVER

OpenSSH daemon

* listens for connections from clients
* forks a new daemon for each incoming connection
* the forked daemons handle key exchange, encryption, authentication, command execution, and data exchange
* makes use of `/etc/hosts.allow` und `/etc/hosts.deny`

## commands

```sh
sudo systemctl start ssh.service
nc -v -z 127.0.0.1 22 # Connection to 127.0.0.1 22 port [tcp/ssh] succeeded!

sudo systemctl restart ssh.service
sudo systemctl reload sshd.service #  decreases the chance that you lose your connection and can’t reconnect.
```

```sh
sudo sshd -t # configtest; only checks the validity of the configuration file and sanity of the keys; useful for updating sshd reliably as configuration options may change
```

```sh
# keep processes running after SSH session disconnects
nohup <long-running-process> & # use nohup to make your process ignore the hangup signal
exit
```

## authorized_keys

* https://www.ssh.com/academy/ssh/authorized-keys-openssh
* https://manpages.debian.org/experimental/openssh-server/authorized_keys.5.en.html

### config

* `command=""` 

  Forces a command to be executed when this key is used for authentication. This is also called **command restriction** or **forced command**. The effect is to limit the privileges given to the key, and specifying this options is often important for implementing the **principle of least privilege**. Without this option, the key grants unlimited access as that user, including obtaining shell access.

* `restrict` disables:

  - port-forwarding
  - agent-forwarding
  - X11-forwarding
  - user-rc
  - pty

#### example borg

**Restrict the use of the SSH keypair** by prepending a forced command to the SSH public key in the remote server’s `authorized_keys` file. 

This example will start Borg in server mode and limit it to a specific filesystem path:

```bash
command="borg serve --restrict-to-path /path/to/repo",restrict <key> <comment>
```

#### example rdiff

Prepend the following in one line with the public key you configure on the machine you like to back up (`/root/.ssh/authorized_keys`):

```sh
command="rdiff-backup --server --restrict-read-only /",from="hostname.or.ip.address.to.allow",no-port-forwarding,no-X11-forwarding,no-pty ssh-rsa AAAA.... and the rest of your key in the same line.
```

This is an example using an RSA key. Change it to your needs.

Optionally, configure the SSH daemon to accept only keys with `forced-commands` for `root`. In `sshd_config` set `PermitRootLogin forced-commands-only`.

This provides quite some security:

- Forced command login only for root, in this case running rdiff-backup in read-only server mode.
- Only authorizes if connection is made from a certain source (host).
- No TCP port and X forwardings allowed.
- No pty assigned.

## config

```sh
sudo xed /etc/ssh/sshd_config # 
```

```sh

StrictModes yes # specifies whether sshd should check file modes and ownership of the user's files and home directory before accepting login. This is normally desirable because novices sometimes accidentally leave their directory or files world-writable; default = yes
```

### keep alive

```sh
TCPKeepAlive yes # specifies whether the system should send TCP keepalive messages to the other side. If they are sent, death of the connection or crash of one of the machines will be properly noticed. However, this means that connections will die if the route is down temporarily, and some people find it annoying. On the other hand, if TCP keepalives are not sent, sessions may hang indefinitely on the server, leaving ''ghost'' users and consuming server resources; default = yes, and the server will notice if the network goes down or the client host crashes. This avoids infinitely hanging sessions.
ClientAliveInterval 0 # if no data has been received from the client after this intervall, sshd will send a message through the encrypted channel to request a response from the client; default = 0 (these messages will not be sent to the client)
ClientAliveCountMax 3 # sets the number of client alive messages which may be sent without sshd receiving any messages back from the client. If this threshold is reached while client alive messages are being sent, sshd will disconnect the client, terminating the session. 
# default = 3 (if ClientAliveInterval is set to 15, unresponsive SSH clients will be disconnected after approximately 45 seconds)
```

It is important to note that the use of client alive messages is very different from TCPKeepAlive. The client alive messages are sent through the encrypted channel and therefore will not be spoofable. The TCP keepalive option enabled by TCPKeepAlive is spoofable. The client alive mechanism is valuable when the client or server depend on knowing when a connection has become inactive.

### change default port

Whatever port you chose, if you do move away from 22, make sure it is below 1024. Under most Unix-a-like setups in their default config, only root (or users in the root group) can listen on ports below 1024, but any user can listen on the higher ports. Running SSH on a higher port increases the chance of a hacked user managing to crash your SSH daemon and replace it with their own or a proxy.

```sh
# don't forget to open ssh port in firewall:
sudo ufw allow in from any to any port 151
```

### authentication

```sh
PermitRootLogin prohibit-password # Login als Root möglich, jedoch nicht per Passwort, sondern nur per Zertifikat
PermitRootLogin no # Login als Root nicht möglich
PermitEmptyPasswords no # 
PasswordAuthentication yes # specifies whether password authentication is allowed
PubkeyAuthentication yes # specifies whether public key authentication is allowed
```

```sh
IgnoreRhosts yes # while not common anymore, rhosts was a weak method to authenticate systems. It defines a way to trust another system simply by its IP address. By default, the use of rhosts is already disabled. Make sure to check if it really is.

# counts, timers, limits:
MaxAuthTries 6 #  specifies the maximum number of authentication attempts permitted per connection. Once the number of failures reaches half this value, additional failures are logged
MaxSessions 10 # specifies the maximum number of open shell, login or subsystem (e.g. sftp) sessions permitted per network connection.  Multiple sessions may be established by clients that support connection multiplexing.  Setting MaxSessions to 1 will effectively disable session multiplexing, whereas setting it to 0 will prevent all shell, login and subsystem sessions while still permitting forwarding
LoginGraceTime 120 # the server disconnects after this time if the user has not successfully logged in. If the value is 0, there is no time limit
# PAM:
ChallengeResponseAuthentication yes # specifies whether challenge-response authentication is allowed (e.g. via PAM)
```

### allow/deny

The allow/deny directives are processed in the following order: DenyUsers, AllowUsers, DenyGroups, AllowGroups

```sh
AllowUsers user1 user2 # if specified, login is allowed only for user names that match one of the patterns; default, login is allowed for all users; if the pattern takes the form user@host then user and host are separately checked, restricting logins to particular users from particular hosts. 
```

### match

```sh
Match User|Group|Host|Adress # Introduces a conditional block. If all of the criteria on the Match line are satisfied, the keywords on the following lines override those set in the global section of the config file, until either another Match line or the end of the file.

Match User unonweb
       X11Forwarding no
       AllowTcpForwarding no
       PermitTTY no
```

### recommendations

```ini
PasswordAuthentication no
PubkeyAuthentication yes
Port 15108
MaxSessions 1
# allows you to enter a limit on the number of sessions that a user can have active
AllowUsers frida payload
PermitEmptyPasswords no
PermitRootLogin no
IgnoreRhosts yes
```

### rescue user

```sh
sudo adduser --no-create-home rescue-1g6sd4lg50b2i57
sudo adduser rescue-1g6sd4lg50b2i57 sudo
```

```ini
Match User rescue-1g6sd4lg50b2i57
       PasswordAuthentication yes
```

### strato

 08.01.2024

```ini
Include /etc/ssh/sshd_config.d/*.conf

Port 151
AddressFamily any
ListenAddress 0.0.0.0
ListenAddress ::

# Authentication

AllowUsers unonweb payload
PasswordAuthentication no
PermitEmptyPasswords no
PermitRootLogin prohibit-password
PubkeyAuthentication yes
IgnoreRhosts yes

LoginGraceTime 2m
StrictModes yes
MaxAuthTries 3
MaxSessions 1
# allows you to enter a limit on the number of sessions that a user can have active
ClientAliveInterval 0
ClientAliveCountMax 3
TCPKeepAlive yes

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
#LogLevel INFO

# Expect .ssh/authorized_keys2 to be disregarded by default in future.
#AuthorizedKeysFile     .ssh/authorized_keys .ssh/authorized_keys2

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
KbdInteractiveAuthentication no

UsePAM yes

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding no
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
PrintMotd no
#PrintLastLog yes
#PermitUserEnvironment no
#Compression delayed

#UseDNS no
#PidFile /run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

# override default of no subsystems
Subsystem       sftp    /usr/lib/openssh/sftp-server
```

sshd verwendet /etc/hosts.allow und /etc/hosts.deny

```ini
vv
```

#### log

```sh
# /var/log/auth.log
grep "Failed password" /var/log/auth.log # 
egrep "Failed|Failure" /var/log/auth.log # 
grep "Failed password" /var/log/auth.log | awk ‘{print $11}’ | uniq -c | sort -nr # displays a list of all IP addresses that tried and failed to log in to the SSH server alongside the number of failed attempts of each IP address
```

# log

```sh
journalctl -u ssh.service -ef
```

# ssh-agent

authentication agent; holds private keys used for public key authentication; usually started in the beginning of an X-session or a login session, and all other windows or programs are started as clients to the ssh-agent program; runs beyond the duration of a local login session, stores unencrypted keys in memory, and communicates with SSH clients using a Unix domain socket.

```sh
# start agent
ssh-agent &
# alternative
eval $(ssh-agent) && ssh-add
alias ssha='eval $(ssh-agent) && ssh-add' # put this in .bashrc
```

## ssh-add

*add private key identities to the authentication agent*;  
The authentication agent must be running.  
If any file requires a passphrase, ssh-add asks for the passphrase from the user.  
The passphrase is read from the user's tty. ssh-add retries the last passphrase if multiple identity files are given.

```sh
ssh-add <option>] <file>
ssh-add -D # deletes all identities from the agent
ssh-add -l # lists fingerprints of all identities currently represented by the agent
ssh-add -L # Lists public key parameters of all identities currently represented by the agent.
ssh-add -c # indicates that added identities should be subject to confirmation before being used for authentication; confirmation is performed by `ssh-askpass`
```

# troubleshooting

* The client uses the private key when connecting not the public key! 

**Troubles with key path, rsync prompting for password when should not:** If using rsync with *sudo*, it looks for key file in */root/.ssh/config* not in */home/user/.ssh/config*, so be sure to copy or link this file to correct location, otherwise ssh and scp will be working fine while rsync will prompt for password.