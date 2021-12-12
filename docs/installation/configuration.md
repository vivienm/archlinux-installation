# Configuration

## Fstab

Générer le fichier `fstab` :

```console
$ genfstab -U /mnt >> /mnt/etc/fstab
```

Vérifier le fichier généré `/mnt/etc/fstab` et le corriger si nécessaire.
Il devrait typiquement se présenter comme suit :

```
# Static information about the filesystems.
# See fstab(5) for details.

# <file system> <dir> <type> <options> <dump> <pass>
# /dev/mapper/arch-root LABEL=ARCH_ROOT
UUID=0c71a3c6-d4d1-48a5-9f41-93a31ff5c042       /               ext4            rw,relatime     0 1

# /dev/nvme0n1p1 LABEL=ESP
UUID=18CA-3DF1          /efi            vfat            rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 2

# /dev/nvme0n1p7 LABEL=BOOT
UUID=DA04-34E3          /boot           vfat            rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 2
```

## Réseau

Copier les fichiers de configuration réseau :

```console
$ cp /etc/systemd/network/*.network /mnt/etc/systemd/network/
$ ln -sf /run/systemd/resolve/stub-resolv.conf /mnt/etc/resolv.conf
```

## Chroot

Ouvrir un shell chroot dans le système nouvellement installé :

```console
$ arch-chroot /mnt
```

## Fuseau horaire

Définir le fuseau horaire :

```console
$ ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime
```

## Langue

Éditer le fichier `/etc/locale.gen` et décommenter les locales `en_US.UTF-8` et `fr_FR.UTF-8`.
Générer ensuite les locales :

```console
$ locale-gen
```

Créer le fichier `/etc/locale.conf` avec pour contenu

```shell
LANG=en_US.UTF-8
```

Créer le fichier `/etc/vconsole.conf` avec pour contenu

```shell
KEYMAP=fr-pc
```

## Nom d'hôte

Entrer le nom de la machine (ne devant comporter que des lettres, des chiffres ou le tiret `-`, et commencer par une lettre) dans le fichier `/etc/hostname`.

## Initramfs

Dans le fichier `/etc/mkinitcpio.conf`, remplacer la ligne

```shell
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)
```

par

```shell
HOOKS=(base systemd autodetect modconf block keyboard sd-vconsole sd-encrypt lvm2 filesystems fsck)
```

Créer l'image initramfs :

```console
$ mkinitcpio -P
```

Ignorer les alertes concernant les firmwares.

## Mot de passe root

Définir un mot de passe pour le compte root :

```console
$ passwd
```

## Microcode

En fonction du processeur, installer l'un des paquets `amd-ucode` ou `intel-ucode`.

Il est possible de vérifier la nature du processeur avec la commande

```console
$ cat /proc/cpuinfo | grep vendor | uniq
```

## Gestionnaire de démarrage

Installer le gestionnaire de démarrage :

```console
$ bootctl --esp-path=/efi --boot-path=/boot install
```

Installer également un hook Pacman pour le mettre à jour automatiquement : créer le répertoire `/etc/pacman.d/hooks` puis le fichier `/etc/pacman.d/hooks/100-systemd-boot.hook` avec pour contenu

```ini
[Trigger]
Type = Package
Operation = Upgrade
Target = systemd

[Action]
Description = Updating systemd-boot
When = PostTransaction
Exec = /usr/bin/bootctl --esp-path=/efi --boot-path=/boot update
```

Éditer le fichier `/efi/loader/loader.conf` avec pour contenu

```
default  arch.conf
timeout  4
console-mode max
editor   no
```

Obtenir l'UUID de la partition LUKS :

```console
$ blkid -l -t LABEL=ARCH
```

Créer le fichier `/boot/loader/entries/arch.conf` avec pour contenu

```
title   Arch Linux
linux   /vmlinuz-linux
initrd  /<CPU>-ucode.img
initrd  /initramfs-linux.img
options root="LABEL=ARCH_ROOT" rw rd.luks.name=<UUID>=arch rd.luks.options=discard
```

en remplaçant

* `<UUID>` par l'UUID de la partition LUKS.
* `<CPU>` par [la nature du processeur](#microcode) (`amd` ou `intel`).

L'option `rd.luks.options=discard` doit être retirée si le système est installé sur un disque dur rotatif.
Il est possible de vérifier la nature du disque avec la commande

```console
$ lsblk -do PATH,ROTA
```
