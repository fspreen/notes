# iptables #
## Persistence ##
```
iptables-save > fw.txt
iptables-restore < fw.txt
```

On Debian-based systems, the package `iptables-persistent` or
`nftables-persistent` is usually available.

## Viewing ##
```
iptables -vnL
```

Optionally, add `--line-numbers` for lines to be numbered for easier reference,
or `-t nat` for e.g. the NAT table.

## Defaults ##
Default tables:

* filter (default table)
* nat
* mangle
* raw

Default chains:

* PREROUTING
* INPUT (traffic to host)
* FORWARD (traffic routed through host)
* OUTPUT (traffic from host)
* POSTROUTING

## Examples ##
These examples are written as one or more invocations of `iptables`, but with
`iptables` left out.  That is to say, each line begins immediately with the
first parameter.  Additionally, these are not full firewall solutions, but
rather snippets that can be added to a larger strategy.

### Minimum Firewall ###
A firewall that can be input quickly and then built upon.  It allows all
outbound traffic.  Inbound traffic is dropped unless it is either part of an
existing connection or ICMP type 8, i.e. "ping".  Any traffic on loopback is
accepted.
```
-A INPUT -m state --state RELATED,ESTABLISHED -j ACCEPT
-P INPUT DROP
-P FORWARD DROP
-A OUTPUT -o lo -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -p icmp --icmp-type 8
```

### SSH Rate Limiting ###
Limits connections to 4 new per 60 seconds on TCP port 22 (i.e. SSH).

```
-A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -m recent --set --name \
      DEFAULT --rsource
-A INPUT -p tcp -m tcp --dport 22 -m state --state NEW -m recent --update \
      --seconds 60 --hitcount 4 --name DEFAULT --rsource -j DROP
-A INPUT -p tcp -m tcp --dport 22 -j ACCEPT
```

### Simple NAT ###
This provides a "masquerade" style NAT to an internal network, similar to a home
router.  It is assumed that the interface to the "outside world" or general
public is `eth0` and that the internal interface is `eth1`.  Additionally, the
internal network is in the block 172.16.0.0/24.

NOTE:  The `nat` table is used here.

```
-t nat -A POSTROUTING -o eth0 -s 12.16.0.0/24 -j MASQUERADE
-A OUTPUT -o lo -j ACCEPT
-A FORWARD -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A FORWARD -o eth0 -j ACCEPT
-A INPUT -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT
-A INPUT -i lo -j ACCEPT
-P INPUT DROP
-P FORWARD DROP
-P OUTPUT ACCEPT
```

Additionally, it is necessary to enable IPv4 forwarding in the kernel.  This can
be done by writing `1` to `/proc/sys/net/ipv4/ip_forward` but this will not
persist across reboots.  Modifying or adding this line to `/etc/sysctl.conf`
will enable the change after a reboot:
```
net.ipv4.ip_forward=1
```

### Port Forwarding ###
The following lines can be added to the NAT example above and demonstrate
different variations.  The operating assumption is that the NATting device has
the internal address of 172.16.0.1.  These examples show:

* port forwarding 25565/tcp to an internal server
* SSH access on different ports (22/tcp internally, 50505 externally)
* HTTP redirect to Squid (3128/tcp) as a transparent proxy

NOTE:  Again, the `nat` table is used here.

```
-t nat -A PREROUTING -i eth0 -p tcp -m tcp --dport 25565 -j DNAT \
        --to-destination 172.16.0.5:25565
-t nat -A PREROUTING -i eth0 -p tcp -m tcp --dport 50505 -j DNAT \
        --to-destination 172.16.0.1:22
-t nat -A PREROUTING -i eth1 -p tcp -m tcp --dport 80 -j DNAT \
        --to-destination 172.16.0.1:3128

-A FORWARD -i eth0 -o eth1 -d 172.16.0.5 -p tcp -m tcp --dport 25565 \
        -m conntrack --ctorigdstport 25565 -j ACCEPT

-A INPUT -i eth1 -p tcp -m tcp --dport 22 -j ACCEPT
-A INPUT -i eth0 -d 172.16.0.1 -p tcp -m tcp --dport 22 \
        -m conntrack --ctorigdstport 50505 -j ACCEPT
-A INPUT -i eth1 -d 172.16.0.1 -p tcp -m tcp --dport 3128 \
        -m conntrack --ctorigdstport 80 -j ACCEPT
```

## References ##
* [Iptables Tutorial](https://www.frozentux.net/iptables-tutorial/iptables-tutorial.html)
