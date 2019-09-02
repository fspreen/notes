# nftables #
`nftables` replaces `iptables` as the Linux packet filtering mechanism.  It
reduces kernel code duplication that was previously part of iptables, ip6tables,
arptables, and ebtables.

The user-space command is `nft`.

## Organization ##
`nftables` organizes into tables, chains, and rules roughly analogous to
`iptables`.  However there are no default/built-in tables or chains; an empty
`nftables` configuration is completely empty.

Tables are specified by address-family and name.  Name is arbitrary.  Address
family is one of `ip`, `ip6`, `inet`, `arp`, `bridge`, or `netdev` which
function as replacements for the old iptables-and-friends commands.  Family
`inet` is combined `ip` and `ip6` which are for IPv4 and IPv6 respectively.

Chains are lists of rules that are applied sequentially.  Rules are specific
match-and-action lines, which may suggest jumping to different chains for
further processing.

### Sets ###

**New** in `nftables` is the concept of a set.  (Introduced in `nftables` 0.7.)
Anonymous sets allow for multi-match syntax in rules similar to shell expansion.
For example, `{22,80,443}` can match any of those TCP ports and can be
specified in the same way.  (This is not limited to ports and can apply to IP
addresses, ICMP types, etc.)

Named sets are created inside tables.  The type of items in a set is specified,
one of `ipv4_addr`, `ipv6_addr`, `ether_addr`, `inet_proto` (like ICMP),
`inet_service` (like TCP port), or `mark`.  Certain flags for behaviors may also
be added, including `constant`, `interval`, and `timeout`.  Items added to a
named set may have a timeout included.  When the timeout expires, the entry is
removed.  When a rule refers to a set, its name is prefixed with `@` (for
example `@blacklist`).

See [Dynamic Blacklist](#dynamic-blacklist) below for an example of using sets
to temporarily block SSH connection spammers.

## Viewing ##
The user-space command `nft` displays the current state of `nftables` on the
system.  Tables can be listed with:
```
nft list tables
```

All rules can be listed with:
```
nft list ruleset
```

**NOTE:** Listing the ruleset will not display e.g. variables defined in any
configuration files.  Additionally, certain numeric values will be replaced with
keywords.  In some cases this replacement happens inappropriately, e.g.
`priority 0` becomes `priority filter` for no clear reason.

## Examples ##
These examples are written as snippets of files that can be input via `nft -f`
or by pasting into `/etc/nftables.conf` on systems that support it.

**NOTE:**  When jumping between chains, a file should list the target chain
before any relevant jump commands.

### Systemd Service ###
`nftables` can be set to run on startup as a systemd service, reading the
ruleset out of `/etc/nftables.conf`.  Arch Linux and Ubuntu Linux (and possibly
others) include this already.  If not, and systemd is installed, create
`/lib/systemd/system/nftables.service` with these contents:

```
[Unit]
Description=Netfilter Tables
Documentation=man:nft(8)
Wants=network-pre.target
Before=network-pre.target

[Service]
Type=oneshot
ExecStart=/usr/bin/nft -f /etc/nftables.conf
ExecReload=/usr/bin/nft flush ruleset ';' include '"/etc/nftables.conf"'
ExecStop=/usr/bin/nft flush ruleset
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
```

### Minimum Firewall ###
A firewall that can be input quickly and then built upon.  This is designed for
a single end host (such as a workstation).  It allows all outbound traffic.
Inbound traffic is dropped unless it is part of an existing connection.  Any
traffic on loopback or port 22 (SSH) is accepted.

```
table inet filter {
    chain input {
        type filter hook input priority 0; policy drop;

        # connection tracking
        ct state { established, related } accept
        ct state invalid drop

        # allow loopback
        iif lo accept

        # allow SSH
        tcp dport 22 accept

        # allow pings
        # note "double" icmp/icmpv6 keywords
        ip protocol icmp icmp type header-request accept
        ip6 nexthdr icmpv6 icmpv6 type header-request accept
    }

    chain forward {
        type filter hook forward priority 0; policy drop;
    }

    chain output {
        type filter hook output priority 0; policy accept;
        oif lo accept
    }
}
```

### ICMP ###
In `nftables 0.7` and possibly other versions, ICMP and ICMPv6 could not be
directly used in `inet` tables without specifying the protocol first.
Workaround looks like:
```
ip protocol icmp icmp type header-request accept
ip6 nexthdr icmpv6 icmpv6 type header-request accept
```

Newer versions should support:
```
icmp type echo-request accept
icmpv6 type echo-request accept
```

(Optionally, add `ct state new` as an additional match condition unless `ct`
conditions have been established by prior rules.)

### Rate Limiting ###
The following rules accomplish similar goals, assuming appropriate `ct` rules
and default policies.  Choose a variant that makes sense depending on the
chain's default policy.
```
# Accept anything up to the rate
tcp dport 22 limit rate 10/second accept
# Drop anything over the rate (note "over" keyword)
tcp dport 22 limit rate over 10/second drop
```

A `burst` clause may optionally be added.  **TODO** look up details and
determine if this is similar to `iptables` or not.
```
tcp dport 22 limit rate 10/second burst 2 packets accept
```

### Dynamic Blacklist ###
Blacklist hosts that are spamming the SSH port.  Note that because sets can hold
only one type of address, IPv4 and IPv6 must each be specified.  In this
example, more than 10 connection attempts per second invokes a ten-minute block.
Attempting another connection during that time restarts the timeout.
```
table inet filter {
    set sshblack4 { type ipv4_addr; flags timeout; }
    set sshblack6 { type ipv6_addr; flags timeout; }

    chain input {
        type filter hook input priority 0;

        # ...

        # Optionally add "ct state new" condition also
        # Note duplicate "tcp tcp" keywords
        ip protocol tcp tcp dport 22 limit rate over 10/second update @sshblack4 {ip saddr timeout 600s}
        ip6 nexthdr tcp tcp dport 22 limit rate over 10/second update @sshblack6 {ip6 saddr timeout 600s}
        ip saddr @sshblack4 drop
        ip6 saddr @sshblack6 drop
    }
}
```

This can be tested by using another host to "spam" the target.  Replace the
target IP and port below.  Expected behavior is to see 25 sets of SSH version
strings, but a 10/second limit will cause this to hang.  (Use `^C` repeatedly to
end it instead of waiting for connection timeouts.)
```sh
for i in $(seq 1 25) ; do echo "" | nc 127.0.0.1 22 ; done
```

### Simple NAT ###
This provides a "masquerade" style NAT to an internal network, similar to a home
router.  It is assumed that the interface to the "outside world" or general
public is `eth0` and that the internal interface is `eth1`.  Additionally, the
internal network is in the block 172.16.0.0/24.

According to the [nftables wiki](https://wiki.nftables.org/wiki-nftables/index.php/Performing_Network_Address_Translation_%28NAT%29#Masquerading)
the `prerouting` chain is still necessary even though it is empty because "you
still have to add the prerouting nat chain, since this translate traffic in the
reply direction" (sic).  This appears to apply to other NAT activities as well.

Note the use of multiple tables here, and remember that table names are
arbitrary strings.

**NOTE:** The `masquerade` keyword can be used _only_ in chains of type `nat`
which in turn cannot be used in tables of type `inet`.  This example is for
IPv4.

```
table ip nat {
    # TODO explanation for priority levels
    # prerouting still necessary (see above)
    chain prerouting { type nat hook prerouting priority -100; policy accept; }
    chain postrouting {
        type nat hook postrouting priority 100;
        policy accept

        oifname "eth0" ip saddr 172.16.0.0/24 masquerade
    }
}

# or inet? (may need ip6 nat table)
table ip filter {
    # IMPORTANT!
    chain forward {
        type filter hook forward priority 0;
        policy drop
        ct state { established, related } accept
        oifname "eth0" accept
    }

    chain input {
        type filter hook input priority 0;
        policy drop
        ct state { established, related } accept
        iifname "lo" accept
    }

    chain output {
        type filter hook input priority 0;
        policy accept
        oifname "lo" accept
    }
}
```

Additionally, it is necessary to enable IPv4 forwarding in the kernel.  This can
be done by writing `1` to `/proc/sys/net/ipv4/ip_forward` but this will not
persist across reboots.  Modifying or adding this line to `/etc/sysctl.conf`
will enable the change after a reboot:
```
net.ipv4.ip_forward=1
```

[ ] What about IPv6?

### Port Forwarding ###
The following structures can be merged with the NAT example above and
demonstrate different variations.  The operating assumption is that the NATting
device has the internal address of 172.16.0.1.  These examples show:

* port forwarding 25565/tcp to an internal server
* SSH access on different ports (22/tcp internally, 50505 externally)
* HTTP redirect to Squid (3128/tcp) as a transparent proxy

```
table ip nat {
    chain prerouting {
        type nat hook prerouting priority 0;

        iifname "eth0" tcp dport 25565 dnat 172.16.0.5:25565
        iifname "eth0" tcp dport 50505 dnat 172.16.0.1:22
        iifname "eth1" tcp dport 80 dnat 172.16.0.1:3128
    }
}

table ip filter {
    chain forward {
        type filter hook forward priority -100;
        policy drop

        iifname "eth0" oifname "eth1" ip daddr 172.16.0.5 tcp dport 25565 \
            ct original proto-dst 25565 accept
    }
    chain input {
        type filter hook input priority 0;
        policy drop
        #...
        iifname "eth1" tcp dport 22 accept
        iifname "eth0" ip daddr 172.16.0.1 tcp dport 22 ct original proto-dst 50505 accept
        iifname "eth1" ip daddr 172.16.0.1 tcp dport 3128 ct original proto-dst 80 accept
    }
}
```

## References ##
* [NFTables Wiki](https://wiki.nftables.org/)
  * [quick reference](https://wiki.nftables.org/wiki-nftables/index.php/Quick_reference-nftables_in_10_minutes)
* [ArchLinux Wiki for nftables](https://wiki.archlinux.org/index.php/Nftables)
* [StackExchange: ICMP in nftables](https://unix.stackexchange.com/questions/408497/nftables-configuration-error-conflicting-protocols-specified-inet-service-v-i?rq=1)
