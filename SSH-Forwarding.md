# SSH Forwarding #
* [Terminology](#terminology)
* [OpenSSH](#openssh)
  * [Backgrounding](#backgrounding)
  * [Existing Connection](#existing-connection)
* [PuTTY](#putty)
  * [PuTTY Existing Connection](#putty-existing-connection)
* [Forwarding Types](#forwarding-types)
  * [Local](#local)
  * [Dynamic](#dynamic)
  * [Remote/Reverse](#remotereverse)
  * [Tunnel](#tunnel)

SSH allows the forwarding or tunneling of network traffic alongside an existing
connection.  Both OpenSSH and PuTTY clients make this possible, allowing for
such setups to be configured during initial connection or added after a
connection is activated.

Forwarding a protected port (e.g. in the 0-1023 range) may require elevated
privileges on the originating host.

## Terminology ##
To reduce confusion, hosts will be referred to by the role they play, using the
following terms:

* **client** - host originating the SSH connection and/or port forwarding
* **server** - host running `sshd` and authenticating the client's credentials
* **remote** - third host involved, typically the forwarding destination
* **$cport, $sport, $rport** - client/server/remote port numbers

* [ ] Where does the remote host name resolve to IP?  Client side?  Server side?

## OpenSSH ##
Syntax and specific examples of forwarding arrangements.

Local forwarding:
```
ssh -L $cport:$remote:$rport user@$server
ssh -L 10080:192.168.0.101:80 user@192.168.0.5
```

Dynamic forwarding:
```
ssh -D $cport $user@$server
ssh -D 10000 user@192.168.0.5
```

Remote/reverse forwarding:
```
ssh -R $sport:$remote:$rport user@server
ssh -R 10080:192.168.0.101:80 user@192.168.0.5
```

### Backgrounding ###
Using the flag `-f` allows the SSH process to be put in the background.  Using
the flag `-N` indicates no command execution on the server (e.g. shell process).
Remember to check that the process is killed when the forwarding is no longer
needed.

### Existing Connection ###
OpenSSH allows an existing connection to be controlled via escape characters.
(See the man page for `ssh` and the section titled ESCAPE CHARACTERS for more
information.)  The escape character is the tilde (`~`) following a newline and
is only available in a pseudo-terminal environment.

The shell prompt does not re-appear after an escape sequence outputs data.
Shell input continues as normal.

To list forwarded connections, use:  `~#`

To add or remove forwards of various types, use `~C` to open a command prompt
for the SSH client.  Commands available are `-h` to list available commands;
`-L, -D, -R` to add the specified forwarding types; and `-KL, -KD, -KR` to
remove a forwarding entry.

## PuTTY ##
Forwarding can be set up prior to connecting by choosing
Connection::SSH::Forwarding in the left side tree.  Add or remove forwards from
the list, using the appropriate radio buttons and controls.

For local forwarding, specify `$cport` in the source port text field and
`$remote:$rport` in the destination field.

For dynamic forwarding, specify the port number in the source port text field.
The destination field will be ignored.

For remote/reverse forwarding, specify `$sport` in the source port field and
`$remote:$rport` in the destination field.

### PuTTY Existing Connection ###
Right-click on the PuTTY window (ideally the title bar) and choose "Settings"
from the menu.  This will open up a window with a subset of the PuTTY
configuration choices, similar to what's available when establishing a new
connection.

## Forwarding Types ##

### Local ###
Local forwarding links a given TCP port on the client host to a specified TCP
port on the remote host.  This works similarly to port forwarding on a router
or the `DNAT` action in `iptables` but only for TCP.

Traffic is encrypted by SSH between the client and server hosts, while the
server host forwards the connection to the remote destination.  The IP address
of the remote host is from the "point of view" of the server; using 127.0.0.1 as
the remote host IP will forward to a port on the server.

### Dynamic ###
Dynamic forwarding allows the SSH connection to act as a SOCKS server when
forwarding traffic.  Applications can be configured to use the specified port on
the client host.  (Most SSH connections allow only traffic from localhost to be
forwarded in this manner.)  The remote host is _not_ specified by SSH, but is
controlled by the forwarded application.

Firefox is one such application that can use a SOCKS tunnel, although the option
is turned off by default.

SOCKS5 allows both TCP and UDP traffic, although it is up to the application to
send traffic appropriately.

### Remote/Reverse ###
Remote forwarding links a given TCP port on the server host to the specified TCP
port on the remote host.  This works similarly to local forwarding, but in
reverse.

Traffic is encrypted by SSH between the client and server hosts, while the
client forwards the connection to the remote destination.  The IP address of the
remote host is from the "point of view" of the client; using 127.0.0.1 as the
remote host IP will forward the server's port to the client.

### Tunnel ###
Some SSH clients can set up a tunnel for traffic forwarding.  This is done via
TUN device, and requires additional network configuration on both client and
server side.  By default, this feature is not enabled on OpenSSH servers.

Consult the OpenSSH `man` page for `ssh` and the section titled "SSH-BASED
VIRTUAL PRIVATE NETWORKS" for more information.

Unknown if this feature is available for Windows hosts.
