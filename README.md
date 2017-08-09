# Nftables

1. [Overview](#overview)
2. [Role Variables](#role-variables)
     * [OS Specific Variables](#os-specific-variables)
     * [Rules Dictionaries](#rules-dictionaries)
3. [Example Playbook](#example-playbook)
4. [Configuration](#configuration)
5. [Development](#development)
5. [License](#license)
6. [Author Information](#author-information)

## Overview

A role to manage Nftables rules and packages.

Highly inspired by [Mike Gleason firewall role][mikegleasonjr firewall github] (3 levels of rules definition and template), thanks ! I hope i haven't complexify his philosophy… ^^

## Role Variables

* **nft_pkg_manage** : If `nftables` package(s) should be managed with this role [default : `true`].
* **nft_pkg_state** : State of new `nftables` package(s) [default : `installed`].
* **nft_main_conf_path** : Main configuration file loaded by systemd unit [default : `/etc/nftables.conf`].
* **nft_main_conf_content** : Template used to generate the previous main configuration file [default : `etc/nftables.conf.j2`].
* **nft_input_conf_path** : Input configuration file include in main configuration file [default : `/etc/nftables.d/inet-input.nft`].
* **nft_input_conf_content** : Template used to generate the previous input configuration file [default : `etc/nftables.d/inet-input.nft.j2`].
* **nft_output_conf_content** : Template used to generate the previous output configuration file [default : `etc/nftables.d/inet-output.nft.j2`].
* **nft_output_conf_path** : Output configuration file include in main configuration file [default : `/etc/nftables.d/inet-output.nft`].
* **nft_define_conf_path** : Vars definition file include in main configuration file [default : `/etc/nftables.d/defines.nft`].
* **nft_define_conf_content** : Template used to generate the previous vars definition file [default : `etc/nftables.d/defines.nft.j2`].
* **nft_sets_conf_path** : Sets and maps definition file include in main configuration file [default : `/etc/nftables.d/inet-sets.nft`].
* **nft_sets_conf_content** : Template used to generate the previous sets and maps definition file [default : `etc/nftables.d/inet-sets.nft.j2`].
* **nft_global_default_rules** : Set default rules for `global` chain. Other chains will jump to `global` before apply their specific rules.
* **nft_global_group_rules** : You can add `global` rules or override those defined by **nft_global_default_rules** for a group.
* **nft_global_host_rules:** : Hosts can also add or override `global` rules.
* **nft_input_default_rules** : Set default rules for `input` chain.
* **nft_input_group_rules** : You can add `input` rules or override those defined by **nft_input_default_rules** for a group.
* **nft_input_host_rules:** : Hosts can also add or override `input` rules.
* **nft_output_default_rules** : Set default rules for `output` chain.
* **nft_output_group_rules** : You can add `output` rules or override those defined by **nft_output_default_rules** for a group.
* **nft_output_host_rules:** : Hosts can also add or override `output` rules.
* **nft_define_default** : Set default vars available in all rules.
* **nft_define_group** : You can add vars or override those defined by **nft_define_default** for groups.
* **nft_define_host** : You can add or override existant vars.
* **nft_service_manage** : If `nftables` service should be managed with this role [default : `true`].
* **nft_service_name** : `nftables` service name [default : `nftables`].

### OS Specific Variables

Please see default value by Operating System file in [vars][vars directory] directory.

* **nft_pkg_list** : The list of package(s) to provide `nftables`.

### Rules Dictionaries

Each type of rules dictionaries will be merged and rules will be applied in the alphabetical order of the keys (the reason to use 000 to 999 as prefix). So :
  * **nft_*_default_rules** : Define default rules for all nodes. You can define it in `group_vars/all`.
  * **nft_*_group_rules** : Can add rules and override those defined by **nft_*_default_rules**. You can define it in `group_vars/webservers`.
  * **nft_*_host_rules** : Can add rules and override those define by **nft_*_default_rules** and **nft_*_group_rules**. You can define it in `host_vars/www.local.domain`.

`defaults/main.yml`:

``` yml
# rules
nft_global_default_rules:
  000 state management:
    - ct state established,related accept
    - ct state invalid drop
nft_global_group_rules: {}
nft_global_host_rules: {}

nft_input_default_rules:
  000 policy:
    - type filter hook input priority 0; policy drop;
  005 global:
    - jump global
  010 drop unwanted:
    - ip daddr @blackhole counter drop
  040 dhcp:
    - udp sport bootps udp dport bootpc limit rate 6/minute accept
  220 ssh:
    - tcp dport ssh ct state new counter accept
nft_input_group_rules: {}
nft_input_host_rules: {}

nft_output_default_rules:
  000 policy:
    - type filter hook output priority 0; policy drop;
  005 global:
    - jump global
  040 dhcp:
    - udp sport bootpc udp dport bootps limit rate 6/minute accept
  050 domain:
    - udp dport domain ct state new accept
nft_output_group_rules: {}
nft_output_host_rules: {}

# define nft vars
nft_define_default:
  broadcast and multicast:
    desc: 'broadcast and multicast'
    name: badcast_addr
    value: '{ 255.255.255.255, 224.0.0.1, 224.0.0.251 }'
nft_define_group: {}
nft_define_host: {}
nft_set_default:
  blackhole:
    - type ipv4_addr;
    - elements = $badcast_addr
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

table inet firewall {
	chain global {
		# 000 state management
		ct state established,related accept
		ct state invalid drop
	}
	include "/etc/nftables.d/inet-sets.nft"
	include "/etc/nftables.d/inet-input.nft"
	include "/etc/nftables.d/inet-output.nft"
}
```

And you get the same result by displaying the ruleset on the host : `$ nft list ruleset` :

```
table inet firewall {
	set blackhole {
		type ipv4_addr
		elements = { 255.255.255.255, 224.0.0.1, 224.0.0.251 }
	}

	chain global {
		ct state established,related accept
		ct state invalid drop
	}

	chain input {
		type filter hook input priority 0; policy drop;
		jump global
		ip daddr @blackhole counter packets 3 bytes 204 drop
		udp sport bootps udp dport bootpc limit rate 6/minute accept
		tcp dport ssh ct state new counter packets 0 bytes 0 accept
	}

	chain output {
		type filter hook output priority 0; policy drop;
		jump global
		udp sport bootpc udp dport bootps limit rate 6/minute accept
		udp dport domain ct state new packets 0 bytes 0 accept
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

* Use default rules with allow ICMP and count dropped input packets :

`group_vars/all` :

``` yaml
nft_global_group_rules:
  002 icmp:
    - ip protocol icmp accept
```

`group_vars/first_group` :

``` yaml
nft_input_group_rules:
  999 count policy packet:
    - counter
```

## Configuration

This role will :
* Install `nftables` on the system.
* Generate a default configuration file which include all following files and loaded by systemd unit.
* Generate input and output rules files include called by the main configuration file.
* Generate vars in a file and sets and maps in another file.
* Restart `nftables` service.

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
