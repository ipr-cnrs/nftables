# Nftables

1. [Overview](#overview)
2. [Role Variables](#role-variables)
     * [OS Specific Variables](#os-specific-variables)
     * [Rules Dictionaries](#rules-dictionaries)
3. [Example Playbook](#example-playbook)
4. [Known Issue](#known-issue)
5. [Configuration](#configuration)
6. [Development](#development)
7. [License](#license)
8. [Author Information](#author-information)

## Overview

A role to manage Nftables rules and packages.

Highly inspired by [Mike Gleason firewall role][mikegleasonjr firewall github] (3 levels of rules definition and template), thanks ! I hope i haven't complexify his philosophy… ^^

## Role Variables

* **nft_enabled** : Enable or disable support for Nftables [default : `true`].
* **nft_pkg_state** : State of new `nftables` package(s) [default : `present`].
* **nft_old_pkg_list** : The list of useless packages to remove (such as Iptables,…) [default : `iptables`].
* **nft_old_pkg_state** : State of old package(s) [default : `absent`].
* **nft_old_pkg_manage** : If old package(s) should be managed with this role [default : `true`].
* **nft_conf_dir_path** : Directory to store the differents Nftables configuration files [default : `/etc/nftables.d`].
* **nft_main_conf_path** : Main configuration file loaded by systemd unit [default : `/etc/nftables.conf`].
* **nft_main_conf_content** : Template used to generate the previous main configuration file [default : `etc/nftables.conf.j2`].
* **nft_input_conf_path** : Input configuration file include in main configuration file [default : `{{ nft_conf_dir_path }}/filter-input.nft`].
* **nft_input_conf_content** : Template used to generate the previous input configuration file [default : `etc/nftables.d/filter-input.nft.j2`].
* **nft_output_conf_path** : Output configuration file include in main configuration file [default : `{{ nft_conf_dir_path }}/filter-output.nft`].
* **nft_output_conf_content** : Template used to generate the previous output configuration file [default : `etc/nftables.d/filter-output.nft.j2`].
* **nft_forward_conf_path** : forward configuration file include in main configuration file [default : `{{ nft_conf_dir_path }}/filter-forward.nft`].
* **nft_forward_conf_content** : Template used to generate the previous forward configuration file [default : `etc/nftables.d/filter-forward.nft.j2`].
* **nft_define_conf_path** : Vars definition file include in main configuration file [default : `{{ nft_conf_dir_path }}/defines.nft`].
* **nft_define_conf_content** : Template used to generate the previous vars definition file [default : `etc/nftables.d/defines.nft.j2`].
* **nft_sets_conf_path** : Sets and maps definition file include in main configuration file [default : `{{ nft_conf_dir_path }}/sets.nft`].
* **nft_sets_conf_content** : Template used to generate the previous sets and maps definition file [default : `etc/nftables.d/sets.nft.j2`].
* **nft_global_default_rules** : Set default rules for `global` chain. Other chains will jump to `global` before apply their specific rules.
* **nft_global_rules** : You can add `global` rules or override those defined by **nft_global_default_rules** for all hosts.
* **nft_global_group_rules** : You can add `global` rules or override those defined by **nft_global_default_rules** and **nft_global_rules** for a group.
* **nft_global_host_rules** : Hosts can also add or override all previours rules.
* **nft__custom_content** : Custom content (tables, include,…) to add in Nftables configuration [default : `''`].
* **nft_input_default_rules** : Set default rules for `input` chain.
* **nft_input_rules** : You can add `input` rules or override those defined by **nft_input_default_rules** for all hosts.
* **nft_input_group_rules** : You can add `input` rules or override those defined by **nft_input_default_rules** and **nft_input_rules** for a group.
* **nft_input_host_rules:** : Hosts can also add or override all previous `input` rules.
* **nft_output_default_rules** : Set default rules for `output` chain.
* **nft_output_rules** : You can add `output` rules or override those defined by **nft_output_default_rules** for all hosts.
* **nft_output_group_rules** : You can add `output` rules or override those defined by **nft_output_default_rules** and **nft_output_rules** for a group.
* **nft_output_host_rules** : Hosts can also add or override all previous `output` rules.
* **nft_forward_default_rules** : Set default rules for `forward` chain.
* **nft_forward_rules** : You can add `forward` rules or override those defined by **nft_forward_default_rules** for all hosts.
* **nft_forward_group_rules** : You can add `forward` rules or override those defined by **nft_forward_default_rules** and **nft_forward_rules** for a group.
* **nft_forward_host_rules** : Hosts can also add or override all previous `forward` rules.
* **nft__forward_table_manage** : If the forward table should be managed [default : `False`].
* **nft__nat_table_manage** : If the nat table should be managed [default : `False`].
* **nft__nat_default_prerouting_rules** : Set default rules for `prerouting` chain of **nat** table.
* **nft__nat_prerouting_rules** : Set rules for `prerouting` chain of **nat** table for all hosts in the Ansible inventory.
* **nft__nat_group_prerouting_rules** : Set rules for `prerouting` chain of **nat** table for hosts in specific Ansible inventory group.
* **nft__nat_host_prerouting_rules** : Set rules for `prerouting` chain of **nat** table for specific hosts the Ansible inventory.
* **nft__nat_prerouting_conf_path** : Prerouting configuration file include in the main configuration [default : `{{ nft_conf_dir_path }}/nat-prerouting.nft`].
* **nft__nat_prerouting_conf_content** : Template used to generate the previous prerouting configuration file [default : `etc/nftables.d/nat-prerouting.nft.j2`].
* **nft__nat_default_postrouting_rules** : Set default rules for `postrouting` chain of **nat** table.
* **nft__nat_postrouting_rules** : Set rules for `postrouting` chain of **nat** table for all hosts in the Ansible inventory.
* **nft__nat_group_postrouting_rules** : Set rules for `postrouting` chain of **nat** table for hosts in specific Ansible inventory group.
* **nft__nat_host_postrouting_rules** : Set rules for `postrouting` chain of **nat** table for specific hosts the Ansible inventory.
* **nft__nat_postrouting_conf_path** : postrouting configuration file include in the main configuration [default : `{{ nft_conf_dir_path }}/nat-postrouting.nft`].
* **nft__nat_postrouting_conf_content** : Template used to generate the previous postrouting configuration file [default : `etc/nftables.d/nat-postrouting.nft.j2`].
* **nft_define_default** : Set default vars available in all rules.
* **nft_define** : You can add vars or override those defined by **nft_define_default** for all hosts.
* **nft_define_group** : You can add vars or override those defined by **nft_define_default** and **nft_define** for a group.
* **nft_define_host** : You can add or override all previous vars.
* **nft_service_manage** : If `nftables` service should be managed with this role [default : `true`].
* **nft_service_name** : `nftables` service name [default : `nftables`].
* **nft_service_enabled** : Set `nftables` service available at startup [default : `true`].
* **nft__service_protect** : If systemd unit should protect system and home [default : `true`].
* **nft__fail2ban_service** : If the Nftables service should also restart the Fail2ban service [default : `False`].
* **nft_merged_groups** : If variables from the hosts Ansible groups should be merged [default : `false`].
* **nft_merged_groups_dir** : The dictionary where the nftables group rules, named like the Ansible groups, are located in [default : `vars/`].
* **nft_debug** : Toggle more verbose output on/off. [default: 'false'].

### OS Specific Variables

Please see default value by Operating System file in [vars][vars directory] directory.

* **nft_pkg_list** : The list of package(s) to provide `nftables`.

### Rules Dictionaries

Each type of rules dictionaries will be merged and rules will be applied in the alphabetical order of the keys (the reason to use 000 to 999 as prefix). So :
  * **nft_*_default_rules** : Define default rules for all nodes. You can define it in `group_vars/all`.
  * **nft_*_rules** : Can add rules and override those defined by **nft_*_default_rules**. You can define it in `group_vars/all`.
  * **nft_*_group_rules** : Can add rules and override those defined by **nft_*_default_rules** and **nft_*_rules**. You can define it in `group_vars/webservers`.
    * If 'nft_merged_groups' is set to true, multiple group rules from the ansible groups will also be merged together.
  * **nft_*_host_rules** : Can add rules and override those define by **nft_*_default_rules**, **nft_*_group_rules** and **nft_*_rules**. You can define it in `host_vars/www.local.domain`.

`defaults/main.yml`:

``` yml
# rules
nft_global_default_rules:
  005 state management:
    - ct state established,related accept
    - ct state invalid drop
nft_global_rules: {}
nft_merged_groups: false
nft_merged_groups_dir: vars/
nft_global_group_rules: {}
nft_global_host_rules: {}

nft_input_default_rules:
  000 policy:
    - type filter hook input priority 0; policy drop;
  005 global:
    - jump global
  010 drop unwanted:
    - ip daddr @blackhole counter drop
  015 localhost:
    - iif lo accept
  200 input udp accepted:
    - udp dport @in_udp_accept ct state new accept
  210 input tcp accepted:
    - tcp dport @in_tcp_accept ct state new accept
nft_input_rules: {}
nft_input_group_rules: {}
nft_input_host_rules: {}

nft_output_default_rules:
  000 policy:
    - type filter hook output priority 0; policy drop;
  005 global:
    - jump global
  015 localhost:
    - oif lo accept
  050 icmp:
    - ip protocol icmp accept
    - ip6 nexthdr icmpv6 counter accept
  200 output udp accepted:
    - udp dport @out_udp_accept ct state new accept
  210 output tcp accepted:
    - tcp dport @out_tcp_accept ct state new accept
nft_output_rules: {}
nft_output_group_rules: {}
nft_output_host_rules: {}

# define nft vars
nft_define_default:
  broadcast and multicast:
    desc: 'broadcast and multicast'
    name: badcast_addr
    value: '{ 255.255.255.255, 224.0.0.1, 224.0.0.251 }'
  input tcp accepted:
    name: in_tcp_accept
    value: '{ ssh }'
  input udp accepted:
    name: in_udp_accept
    value: 'none'
  output tcp accepted:
    name: out_tcp_accept
    value: '{ http, https, hkp }'
  output udp accepted:
    name: out_udp_accept
    value: '{ bootps, domain, ntp }'
nft_define: {}
nft_define_group: {}
nft_define_host: {}

# sets and maps
nft_set_default:
  blackhole:
    - type ipv4_addr;
    - elements = $badcast_addr
  in_tcp_accept:
    - type inet_service; flags interval;
    - elements = $in_tcp_accept
  in_udp_accept:
    - type inet_service; flags interval;
  out_tcp_accept:
    - type inet_service; flags interval;
    - elements = $out_tcp_accept
  out_udp_accept:
    - type inet_service; flags interval;
    - elements = $out_udp_accept
nft_set: {}
nft_set_group: {}
nft_set_host: {}
```

Those default will generate the following configuration :
```
#!/usr/sbin/nft -f
# Ansible managed

# clean
flush ruleset

include "/etc/nftables.d/defines.nft"

table inet filter {
	chain global {
		# 000 state management
		ct state established,related accept
		ct state invalid drop
	}
	include "/etc/nftables.d/sets.nft"
	include "/etc/nftables.d/filter-input.nft"
	include "/etc/nftables.d/filter-output.nft"
}
```

And you can get all rules and definitons by displaying the ruleset on the host : `$ nft list ruleset` :

```
table inet filter {
	set blackhole {
		type ipv4_addr
		elements = { 255.255.255.255, 224.0.0.1, 224.0.0.251}
	}

	set out_tcp_accept {
		type inet_service
		flags interval
		elements = { http, https, hkp}
	}

	set out_udp_accept {
		type inet_service
		flags interval
		elements = { domain, bootps, ntp}
	}

	chain global {
		ct state established,related accept
		ct state invalid drop
	}

	chain input {
		type filter hook input priority 0; policy drop;
		jump global
		ip daddr @blackhole counter packets 0 bytes 0 drop
		iif "lo" accept
		udp dport @in_udp_accept ct state new accept
		tcp dport @in_tcp_accept ct state new accept
	}

	chain output {
		type filter hook output priority 0; policy drop;
		jump global
		oif "lo" accept
		ip protocol icmp accept
		udp dport @out_udp_accept ct state new accept
		tcp dport @out_tcp_accept ct state new accept
	}
}
```

## Example Playbook

* Manage Nftables with defaults vars :

``` yml
- hosts: serverXYZ
  roles:
    - role: ipr-cnrs.nftables
```

* Override some of the default defined sets:

``` yml
- hosts: serverXYZ
  vars:
    - nft_define:
      input tcp accepted:
        desc: Custom SSH port and torrent
        name: in_tcp_accept
        value: '{ 2201, 6881 }'
      input udp accepted:
        desc: torrent
        name: in_udp_accept
        value: '{ 6881 }'
  roles:
    - role: ipr-cnrs.nftables
```

* Use default rules with allow incoming ICMP and count dropped input packets :

`group_vars/first_group` :

``` yaml
nft_input_group_rules:
  020 icmp:
    - ip protocol icmp icmp type echo-request ip length <= 84 counter limit rate 1/minute accept
  999 count policy packet:
    - counter
```

* Use merged group rules from multiple ansible groups:

``` yml
- hosts: serverXYZ
  vars:
    nft_merged_groups: true
    nft_merged_groups_dir: vars/
  roles:
    - role: ipr-cnrs.nftables
```

And put the rules inside the "vars" folder named after your ansible groups of the server:

`vars/first_group` :

``` yaml
nft_input_group_rules:
  020 icmp:
    - ip protocol icmp icmp type echo-request ip length <= 84 counter limit rate 1/minute accept
  999 count policy packet:
    - counter
```

`vars/second_group` :

``` yaml
nft_input_group_rules:
  021 LAN:
    - iif eth0 accept
```

These rulesets from the two groups will be merged if the host has the two groups as ansible roles.

## Known Issue

* The 10 minutes delay at the first run is finally fixed by allowing the host to reset SSH connection (flags `rst, psh | ack`) (see #1).

## Configuration

This role will :
* Install `nftables` on the system.
* Enable `nftables` service by default at startup.
* Generate a default configuration file which include all following files and loaded by systemd unit.
* Generate input and output rules files include called by the main configuration file.
* Generate vars in a file and sets and maps in another file.
* (re)Start `nftables` service at first run.
* Reload `nftables` service at next runs to avoid to let the host without firewall rules due to invalid syntax.

## Development

This source code comes from our [Gogs instance][nftables source] and the [Github repo][nftables github] exist just to be able to send the role to Ansible Galaxy…

But feel free to send issue/PR here :)

Thanks to this [hook][gogs to github hook], Github automatically got updates from our [Gogs instance][nftables source] :)

## License

[WTFPL][wtfpl website]

## Author Information

Jérémy Gardais
* Source : [on IPR's Gogs][nftables source]
* [IPR][ipr website] (Institut de Physique de Rennes)

[gogs to github hook]: https://stackoverflow.com/a/21998477
[nftables source]: https://git.ipr.univ-rennes1.fr/cellinfo/ansible.nftables
[nftables github]: https://github.com/ipr-cnrs/nftables
[wtfpl website]: http://www.wtfpl.net/about/
[ipr website]: https://ipr.univ-rennes1.fr/
[mikegleasonjr firewall github]: https://github.com/mikegleasonjr/ansible-role-firewall
