# Installation

Installer les paquets essentiels :

```console
$ pacstrap /mnt base linux linux-firmware lvm2 vim
```

Le paquet `linux-firmware` peut être omis dans une machine virtuelle.

Si l'ordinateur ne dispose pas d'une liaison Ethernet, installer également `iwd`.
