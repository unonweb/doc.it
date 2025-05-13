# netcat | nc

**arbitrary TCP and UDP connections**

```sh
nc <options> <dest> <port>
nc -v -z 127.0.0.1 22 # Connection to 127.0.0.1 22 port [tcp/ssh] succeeded!

nc -z # Only scan for listening daemons, without sending any data to them.
nc -N # The connection may be terminated using an EOF (‘^D’)

nc -p <port> # Specify the source port nc should use, subject to privilege restrictions and availability.

nc -l # Listen for an incoming connection rather than initiating a connection to a remote host.  The destination and port to listen on can be specified either as non-optional arguments, or with options -s and -p respectively.
```

```sh
nc -l 1234 # nc is now listening on port 1234 for a connection
nc 127.0.0.1 1234 # there should now be a connection between the ports

# There should now be a connection between the ports. Anything typed at the second console will be concatenated to the first, and vice-versa. After the connection has been set up, nc does not really care which side is being used as a ‘server’ and which side is being used as a ‘client’. The connection may be terminated using an EOF (‘^D’), as the -N flag was given.
```
