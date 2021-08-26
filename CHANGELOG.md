## v2.0.1

### Added
* Molecule tests for Gentoo (many thanks to @VTimofeenko ! − PR #25).

### Fixed
* Separate repositories update from installation task (fix #24).

## v2.0.0

### Added
* New examples usecases (mostly for playbooks) in README.md.
* New rules (disable by default) can be define in *forward* chain (thanks to
  @p-rintz − PR #14).
* Possibility to toggle file's backup (thanks to @p-rintz − PR #15).
* Gentoo-specific variables (thanks to @VTimofeenko − PR #22).
* Ability to specify nft binary path through **nft__bin_location** (thanks to @VTimofeenko − PR #22).
* Manage Fail2ban in the "systemd way" (thanks to @FinweVI − PR #16).
* Molecule tests (on Archlinux, Ubuntu, CentOS, Debian and Fedora) (many thanks to @kravietz ! − PR #23).
* Support for Debian Bullseye (everything should now works fine).

### Removed
* Remove everything related to **in_udp_accept** (see conversation in
  [Github PR #13](https://github.com/ipr-cnrs/nftables/pull/13)).
  Cause it was empty by default and the role currently doesn't manage it very
  well. Take a look to new examples in README.md to find your preferred solution
  (re-adding it, new simple/multi-ports filter rule,…).

### Fixed
* Ansible-lint: Fix line longer than 160 chars.
* Start nftables systemd unit earlier (thanks to @kravietz − PR #19).
* Ensure to disable nftables systemd unit from old target (PR #20).
* Move systemd "Protect" options for nftables to specific override.conf file (PR #20).

## v1.7.0

### Features
* Allow to merge group variables with **nft_merged_groups** (#11 #12).

### Enhancements
* Debug var with **nft_debug** (useful to set up merging group variables).
* Extra example to override default variables.

### Fix
* Add missing ICMPv6 rule.

## v1.6.0

### Features
* Able to manage a new NAT table (with prerouting and postrouting chains).
* Block ipv6 multicast by default.

### Enhancements
* Clean tasks name and comments in tasks/main.yml file.
* Order and clean comments in defaults/main.yml file.
* Reload rules instead of restart to avoid to loose rulebase due to invalid syntax (#3 Github).

### Fix
* Fix deprecation warning with ansible 2.7: Invoking "apt" only once while
  using a loop via squash_actions is deprecated.
* Turn nft_old_pkg_list into a list.
* Add libiptc0 (iptables dependency) to the list of old package to remove.
* The 10 minutes delay at first run (#1)!

## v1.5.0

### Enhancements
* Add a variable to disable "Protect" instructions in systemd unit.
* Improve vars description/comments in default/main.yml.
* Add a variable to manage custom content (table, include,…).

## v1.4.1

### Fix
* Set empty dependencies line to fix Galaxy warning.
* Add possibility to restart Fail2ban service.
* Use to_nice_json to manage packages list.
* Fix E405 Remote package tasks should have a retry.

## v1.4.0

### Enhancements
* Set a variable to enable/disable the support of Nftables.
* Move two tasks in systemd handler (try to fix #1).
* Add a additionnal level for all vars for all hosts (group_vars/all).

### Fix
* Deprecation warning for state "installed".
* The role now might require Ansible 2.5 (available in Debian Stable backports).

## v1.3.1

### Fix
* Reload systemd daemons only if unit file change.

## v1.3.0

### Features
* Provide the systemd unit.

## v1.2.3
* Rename firewall table to filter table (most use on Debian).

## v1.2.2

### Fix
* Set's name can't exceed 15 characters !

## v1.2.1

### Features
* Allow icmpv6 outgoing traffic.

## v1.2.0

### Features
* Ensure to remove old packages (iptables,…).

### Fixes
* Ensure to create the the directory to store the differents configuration files (/etc/nftables.d).

## v1.1.0

### Features
* Manage nftables service at startup.
* Rollback to inet family to manage both ipv4 and ipv6.
* To allow multiple ports/range ports, it's possible to redifine vars or add a rule in a dict.

### Default Rules
* Use more sets and vars definitions for input/output to avoid multiple rules.
* Allow outgoing icmp.
* Remove DHCP incoming packets. The connection is started by the host, don't need incoming rule.
* Allow outgoing OpenPGP HTTP requests.

## v1.0.0

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
