# Nftables

1. [Overview](#overview)
2. [Role Variables](#role-variables)
     * [OS Specific Variables](#os-specific-variables)
3. [Example Playbook](#example-playbook)
4. [Configuration](#configuration)
5. [Development](#development)
5. [License](#license)
6. [Author Information](#author-information)

## Overview

A role to manage Nftables rules and packages.

## Role Variables

* **nft_pkg_manage** : If `nftables` package(s) should be managed with this role [default : `true`].
* **nft_pkg_state** : State of new `nftables` package(s) [default : `installed`].
* **nft_main_conf_path** : Main configuration file loaded by systemd unit [default : `/etc/nftables.conf`].
* **nft_main_conf_content** : Template used to generate the previous main configuration file [default : `etc/nftables.conf.j2`].

### OS Specific Variables

Please see default value by Operating System file in [vars][vars directory] directory.

* **nft_pkg_list** : The list of package(s) to provide `nftables`.

## Example Playbook

* Manage Nftables with defaults vars :

``` yml
- hosts: serverXYZ
  roles:
    - role: ipr-cnrs.nftables
```

## Configuration

This role will :
* Install `nftables` on the system.
* Generate a default configuration file loaded by systemd unit.

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
