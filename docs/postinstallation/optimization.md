# Optimisation

## SSD

Vérifier si certains périphériques supportent le TRIM :

```console
$ lsblk --discard
```

Le cas échéant, activer un TRIM périodique des disques :

```console
$ systemctl enable fstrim.timer
```

## TLP

Sur laptop, installer TLP pour optimiser la durée de vie de la batterie :

```console
$ pacman -S tlp
$ systemctl enable --now tlp.service
```
