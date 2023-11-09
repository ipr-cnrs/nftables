# Nftables

1. [Overview](#overview)
1. [Role Variables](#role-variables)
     * [OS Specific Variables](#os-specific-variables)
     * [Rules Dictionaries](#rules-dictionaries)
1. [Examples](#examples)
     * [With playbooks](#with-playbooks)
     * [With group_vars and host_vars](#with-group_vars-and-host_vars)
1. [Configuration](#configuration)
     * [Fail2ban integration](#fail2ban-integration)
1. [Development](#development)
1. [License](#license)
1. [Author Information](#author-information)

## Overview

A role to manage Nftables rules and packages.

Highly inspired by [Mike Gleason firewall role][mikegleasonjr firewall github]
(3 levels of rules definition and template), thanks ! I hope i haven't
complexify his philosophy… (I'm pretty sure, i now did complexify it :D) ^^

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
* **nft_merged_groups** : If variables from the hosts Ansible groups should be merged [default : `false`].
* **nft_merged_groups_dir** : The dictionary where the nftables group rules, named like the Ansible groups, are located in [default : `vars/`].
* **nft_debug** : Toggle more verbose output on/off. [default: 'false'].

### OS Specific Variables

Please see default value by Operating System file in [vars][vars directory] directory.

* **nft_pkg_list** : The list of package(s) to provide `nftables`.
* **nft__bin_location** : Path to `nftables` executable. [default : `/usr/sbin/nft`]

### Rule Templates

The `nft_templates` dictionary contains a library of useful rules, intended to be standardized and ready to use in your firewall. For
example `{{ nft_templates.allow_mdns }}` will expand to the following rules, covering mDNS for both IPv4 and IPv6:

```
  - meta l4proto udp ip6 daddr ff02::fb    udp dport mdns counter accept comment "mDNS IPv6 service discovery"
  - meta l4proto udp ip  daddr 224.0.0.251 udp dport mdns counter accept comment "mDNS IPv4 service discovery"
```

Intended usage in custom rule sets:

```
nft_host_input_rules:
  ...
  010 allow mdns: "{{ nft_templates.allow_mdns }}"
```

Among others, the `nft_templates` dictionary contains rules implementing full recommendations for forwarding and input traffic firewalls as defined in [RFC 4890](https://www.rfc-editor.org/rfc/rfc4890), [RFC 7126](https://www.rfc-editor.org/rfc/rfc7126) and [RFC 9288](https://www.rfc-editor.org/rfc/rfc9288). For details and examples see [defaults/main.yml](defaults/main.yml#L662).

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

## Examples

### With playbooks

<details>
  <summary>Manage Nftables with defaults vars (<i>click to expand</i>)</summary>
  <!-- have to be followed by an empty line! -->

``` yml
- hosts: serverXYZ
  roles:
    - role: ipr-cnrs.nftables
```
</details>

<details>
  <summary>Add a new <b>simple</b> filter rule for incoming traffic (eg. 1 port for UDP/torrent) (<i>click to expand</i>)</summary>

``` yml
- hosts: serverXYZ
  vars:
      nft_input_rules:
        400 input torrent accepted:
          - udp dport 6881 ct state new accept
  roles:
    - role: ipr-cnrs.nftables
```
* **nft_input_group_rules** or **nft_input_host_rules** variables can
  also be used.
* The weight (`400`) allow to order all merged rules (from
  <b>nft_input_*rules</b> dictionaries).
* The text following the weight (`input torrent accepted`) is a small
  description that will be added as a comment in **nft_input_conf_path** file
  on the remote host.
</details>

<details>
  <summary>Add a new <b>multi-ports</b> filter rule for incoming traffic (eg. TCP/http, https, http-alt,…) (<i>click to expand</i>)</summary>

``` yml
- hosts: serverXYZ
  vars:
      nft_input_rules:
        400 input http accepted:
          - tcp dport { 80, 443, 8080-8082 } ct state new accept
  roles:
    - role: ipr-cnrs.nftables
```
* **nft_input_group_rules** or **nft_input_host_rules** variables can
  also be used.
* The weight (`400`) allow to order all merged rules (from
  <b>nft_input_*rules</b> dictionaries).
* The text following the weight (`input http accepted`) is a small
  description that will be added as a comment in **nft_input_conf_path** file
 on the remote host.
* In this case, brackets are useful and define a anonymous set. For a single
  element (port, IP address,…), brackets are overkill and the singleton
  definition is enought.
</details>

<details>
  <summary>Add a new rule with a variable (<i>click to expand</i>)</summary>

Nftables variables can be useful if you define somes generic rules for all hosts
with such variables (called with **$**) and override variable's value for some
groups or hosts.
``` yml
- hosts: serverXYZ
  vars:
    - nft_define_group:
        input http accepted:
          desc: HTTP and HTTPS
          name: in_http_accept
          value: '{ 80, 443 }'
      nft_input_group_rules:
        400 input http accepted:
          - tcp dport $in_http_accept ct state new accept
  roles:
    - role: ipr-cnrs.nftables
```
1. Add a new variable with **define** for HTTP ports.
1. Add a new rule for incoming traffic and use the previous defined variable.
1. Result of `nft list ruleset` on the remote host will be :
   ``` bash
   table inet filter {
           …

           chain input {
                   …
                   tcp dport { http, https } ct state new accept
                   …
           }
           …
   }
   ```
   * No mention of `$in_http_accept` variable.
* **nft_define** or **nft_define_host** variables can also be used.
* **nft_input_rules** or **nft_input_host_rules** variables can also be used.
* The weight (`400`) allow to order all merged rules (from
  <b>nft_input_*rules</b> dictionaries).
* The text following the weight (`input http accepted`) is a small description
  that will be added as a comment in **nft_input_conf_path** file on
  the remote host.
</details>

<details>
  <summary>Add a new rule with a named set (<i>click to expand</i>)</summary>

Quite similar to Nftables variables, **named set** can be useful if you define
somes generic rules and sets (eg. for all hosts) and override only the set in
some case (eg. for a group or some hosts).

In addition to variables, it is possible to add content to named sets on the
fly from the host without completely rewrite the rule.
``` yml
- hosts: serverXYZ
  vars:
      nft_set_group:
        in_udp_accept:
          - type inet_service; flags interval;
          - elements = { 6881-6887, 6889 }
      nft_input_group_rules:
        200 input udp accepted:
          - udp dport @in_udp_accept ct state new accept
  roles:
    - role: ipr-cnrs.nftables
```
1. Add a new named set with <b>nft_set_group</b> dictionary (eg. for torrent ports).
1. Add a new rule for incoming traffic and use the previous defined set.
1. On the remote host, if you try to add a port to this set : `nft add element inet filter in_udp_accept \{ 6999 \}`
1. Result of `nft list ruleset` on the remote host will now be :
   ``` bash
   table inet filter {
           …
           set in_udp_accept {
                   type inet_service
                   flags interval
                   elements = { 6881-6887, 6889, 6999 }
           }
           chain input {
                   …
                   udp dport @in_udp_accept ct state new accept
                   …
           }
           …
   }
   ```
* **nft_set** or **nft_set_host** variables can also be used.
* **nft_input_rules** or **nft_input_host_rules** variables can also be used.
* The weight (`200`) allow to order all merged rules (from
  <b>nft_input_*rules</b> dictionaries).
* The text following the weight (`input upd accepted`) is a small description
  that will be added as a comment in **nft_input_conf_path** file on
  the remote host.
</details>

<details>
  <summary>Override a default rule with 2 new rules (<i>click to expand</i>)</summary>

``` yml
- hosts: serverXYZ
  vars:
      nft_input_host_rules:
        050 icmp:
          - ip protocol icmp  ip saddr != 192.168.0.0/24  counter  drop
          - ip protocol icmp  icmp type echo-request  ip length <= 84  counter  limit rate 10/minute  accept
  roles:
    - role: ipr-cnrs.nftables
```
1. Get rule description from `defaults/main.yml` file (eg. `050 icmp`).
1. Drop any ICMP request that doesn't come from 192.168.0.0 network.
1. Ensure the request is less or equal to 84 bytes and set up a limit
   to 10 requests per minute.
* **nft_input_rules** or **nft_input_group_rules** variables can also be used.
* The weight (`050`) allow to order all merged rules (from
  <b>nft_input_*rules</b> dictionaries).
* The text following the weight (`icmp`) is a small description that
  will be added as a comment in **nft_input_conf_path** file on
  the remote host.
</details>

<details>
  <summary>Override some of the default defined sets (<i>click to expand</i>)</summary>

``` yml
- hosts: serverXYZ
  vars:
    - nft_define:
      input tcp accepted:
        desc: Custom SSH port and torrent
        name: in_tcp_accept
        value: '{ 2201, 6881 }'
  roles:
    - role: ipr-cnrs.nftables
```
1. Get item name (eg. `input tcp accepted`) and variable name
   (eg. `in_tcp_accept`) from `defaults/main.yml` file.
1. Set a new value (eg. `'{ 2201, 6881 }'`).
1. You can add a `desc` attribute that will be set as a comment in
   **nft_input_conf_path** file on the remote host.
* **nft_define_group** or **nft_define_host** variables can also be used.
</details>

<details>
  <summary>Override all default rules (eg. for outgoing traffic) (<i>click to expand</i>)</summary>

If the default rules are too permissive, if you already override most of them,…
In some case, i guess, it can be interesting to redefine the default variable :
``` yml
- hosts: serverXYZ
  vars:
      nft_output_default_rules:
        000 policy:
          - type filter hook output priority 0; policy drop;
        005 state management:
          - ct state established,related accept
          - ct state invalid drop
        015 localhost:
          - oif lo accept
        050 my rule for XXX hosts and services:
          - tcp dport 2000  ip saddr { xxx.xxx.xxx.xxx, yyy.yyy.yyy.yyy }  ct state new  accept
        250 reset-ssh:  # allow the host to reset SSH connections to avoid 10 min delay from Ansible controller
          - tcp sport ssh tcp flags { rst, psh | ack } counter accept
  roles:
    - role: ipr-cnrs.nftables
```
* At least, don't forget to :
  1. set a default `policy`.
  1. manage already `established` state.
  1. accept `rst, psh | ack` flags for **ssh** to avoid a 10 minutes delay at
     the first run of this Nftables role (see #1).
* Then add your own rules with the wanted weight to order all merged rules (from
  <b>nft_output_*rules</b> dictionaries) and descriptions.
</details>

<details>
  <summary>Remove a default rule (<i>click to expand</i>)</summary>

``` yml
- hosts: serverXYZ
  vars:
      nft_output_host_rules:
        210 output tcp accepted:
          -
  roles:
    - role: ipr-cnrs.nftables
```
1. Get rule description from `defaults/main.yml` file (`210 output tcp accepted`).
1. The default policy for outgoing traffic (**drop**) will now be applied to
   ports defined in **out_tcp_accept** variable. Be sure of what you are doing.
1. The rule will no longer be present in `nft list ruleset` result, just a
   comment will remain (`210 output tcp accepted`) in **nft_output_conf_path**
   file on the remote host.
* **nft_output_rules** or **nft_output_group_rules** variables can also be used.
* The weight (`210`) allow to order all merged rules (from
  <b>nft_output_*rules</b> dictionaries).
</details>

### With group_vars and host_vars

<details>
  <summary>Use default rules and allow, for <b>first_group</b>, incoming ICMP and count both ICMP and default policy (<b>drop</b>) packets (<i>click to expand</i>)</summary>

`group_vars/first_group` :

``` yaml
nft_input_group_rules:
  020 icmp:
    - ip protocol icmp icmp type echo-request ip length <= 84 counter limit rate 1/minute accept
  999 count policy packet:
    - counter
```
</details>

<details>
  <summary>Use merged group rules from multiple ansible groups (<i>click to expand</i>)</summary>

1. Enable to merge group's variables :
    ``` yml
    - hosts: serverXYZ
      vars:
        nft_merged_groups: true
        nft_merged_groups_dir: vars/
      roles:
        - role: ipr-cnrs.nftables
    ```
1. Put extra rules inside the "vars" folder named after your ansible groups for serverXYZ :
   * `vars/first_group` :
       ``` yaml
       nft_input_group_rules:
         020 icmp:
           - ip protocol icmp icmp type echo-request ip length <= 84 counter limit rate 1/minute accept
         999 count policy packet:
           - counter
       ```
   * `vars/second_group` :
       ``` yaml
       nft_input_group_rules:
         021 LAN:
           - iif eth0 accept
       ```
1. These rulesets, from the two groups, will be merged if the host is a member
   of these groups.
</details>

## Configuration

This role will :
* Install `nftables` on the system.
* Enable `nftables` service by default at startup.
* Generate a default configuration file which include all following files and
  loaded by systemd unit.
* Generate input and output rules files include called by the main configuration file.
* Generate vars in a file and sets and maps in another file.
* Ensure `nftables` is started and enabled on boot
* (re)Start `nftables` service at first run or when systemd units are modified
* Reload `nftables` service at next runs to avoid to let the host without firewall
  rules due to invalid syntax.

### Fail2ban integration

Before Debian Bullseye, systemd unit for Fail2ban doesn't come with a decent
integration with Nftables.
This role will create override file for `fail2ban` unit, unless
`nft_fail2ban_service_override` is set to `false`. Default is to add it even if
it's not (yet) available on the host. This ensures :
* The `fail2ban` unit is started after the `nftables` unit.
* The `fail2ban` unit is restarted when `nftables` unit restarts.

## Development

This source code comes from our [Gitea instance][nftables source] and the
[Github repo][nftables github] exist just to be able to send the role to Ansible
Galaxy…

But feel free to send issue/PR here :)

Thanks to this [hook][gogs to github hook], Github automatically got updates from our [Gitea instance][nftables source] :)

## License

[WTFPL][wtfpl website]

## Author Information

Jérémy Gardais
* [IPR][ipr website] (Institut de Physique de Rennes)

[gogs to github hook]: https://stackoverflow.com/a/21998477
[nftables source]: https://github.com/ipr-cnrs/nftables
[nftables github]: https://github.com/ipr-cnrs/nftables
[wtfpl website]: http://www.wtfpl.net/about/
[ipr website]: https://ipr.univ-rennes.fr/
[mikegleasonjr firewall github]: https://github.com/mikegleasonjr/ansible-role-firewall
