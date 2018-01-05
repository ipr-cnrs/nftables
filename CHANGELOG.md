
## v1.2.2

### Fix
* Set's name can't exceed 15 characters !

## v1.2.1

### Features
* Allow icmpv6 outgoing traffic.

## v1.2

### Features
* Ensure to remove old packages (iptables,…).

### Fixes
* Ensure to create the the directory to store the differents configuration files (/etc/nftables.d).

## v1.1

### Features
* Manage nftables service at startup.
* Rollback to inet family to manage both ipv4 and ipv6.
* To allow multiple ports/range ports, it's possible to redifine vars or add a rule in a dict.

### Default Rules
* Use more sets and vars definitions for input/output to avoid multiple rules.
* Allow outgoing icmp.
* Remove DHCP incoming packets. The connection is started by the host, don't need incoming rule.
* Allow outgoing OpenPGP HTTP requests.

## v1.0

### Features
* Install `nftables` package for Debian based distros.
* Generate `nftables` main configuration file.
* Manage global, input and output chains with three dicts.
* Manage vars, sets and maps definition file.
* Restart `nftables` service.

### Default Rules
* Drop blackhole set input packets.
* Allow localhost traffic.
* Allow DHCP traffic.
* Allow SSH input (otherwise Ansible won't work).
* Allow DNS request.
