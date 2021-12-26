# Pré-installation

## Périphérique de démarrage

Télécharger la [dernière image ISO d'Arch Linux](https://archlinux.org/download/) et la graver sur une clé USB :

```console
$ dd if=archlinux-x86_64.iso of=/dev/$DEVICE
$ sync
```

Insérer la clé USB dans l'ordinateur et démarrer dessus.
Cela se fait typiquement via le menu de démarrage (généralement accessible depuis l'écran de démarrage en appuyant sur la touche `F10`, `F12` ou `Esc`) ou depuis l'écran de configuration du BIOS (généralement accessible en appuyant sur la touche `F2`).

## Réglage du clavier

Passer le clavier en AZERTY :

```console
$ loadkeys fr-pc
```

(Taper `loqdkeys fr)pc`.)

## Vérification du mode de démarrage

Vérifier que le PC est bien démarré en mode UEFI :

```console
$ ls /sys/firmware/efi/efivars
```

## Connexion internet

### Ethernet

Si l'ordinateur dispose d'une liaison Ethernet, vérifier avec `ip a` que l'interface réseau est correctement reconnue et configurée.

### Wifi

Si l'ordinateur ne dispose pas d'une liaison Ethernet, se connecter au réseau wifi avec `iwctl`.

Afficher les interfaces réseau disponibles :

```console
$ iwctl device list
```

Afficher les réseaux wifi disponibles :

```console
$ iwctl station $DEVICE get-networks
```

Se connecter à un réseau wifi :

```console
$ iwctl station $DEVICE connect $SSID
```

### Vérification

Vérifier que la connexion internet fonctionne :

```console
$ ping archlinux.org
```

## Serveur SSH

Si l'on dispose d'un autre ordinateur, il peut être plus confortable d'effectuer l'essentiel de l'installation en SSH.
Il faut pour cela définir un mot de passe pour le compte `root` et démarrer le service `sshd` :

```console
$ passwd root
$ systemctl start sshd.service
```

La commande `ip a` permet de déterminer l'IP de la machine sur le réseau local, afin de s'y connecter.

## Horloge

Régler l'heure :

```console
$ timedatectl set-ntp true
```

## Partitionnement et formatage

Identifier le périphérique d'installation avec

```console
$ lsblk -do PATH
```

S'assurer qu'il dispose d'une table des partitions au format GPT.

```console
$ gdisk -l $DEVICE
Partition table scan:
  [...]
  GPT: present
```

Dans le cas contraire, effacer la table des partitions (attention, toutes les données seront perdues !) et la recréer :

```console
$ gdisk $DEVICE
Command (? for help): o
This option deletes all partitions and creates a new protective MBR.
Proceed? (Y/N): Y

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to $DEVICE
The operation has completed successfully.
```

### EFI

S'assurer que la périphérique dispose d'une partition EFI.

La créer si elle n'existe pas:

```console
$ gdisk $DEVICE
Command (? for help): n
Partition Number: <default>
First sector: <default>
Last sector: +256M
Hex Code: ef00

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to $DEVICE
The operation has completed successfully.
```

La formater en FAT 32 :

```console
$ mkfs.fat -F32 -n ESP /dev/$EFI_PARTITION
```

### Démarrage

Créer la partition de démarrage :

```console
$ gdisk $DEVICE
Command (? for help): n
Partition Number: <default>
First sector: <default>
Last sector: +512M
Hex Code: ea00

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to $DEVICE
The operation has completed successfully.
```

La formater en FAT 32 :

```console
$ mkfs.fat -F32 -n BOOT /dev/$BOOT_PARTITION
```

### Système

Créer la partition système :

```console
$ gdisk $DEVICE
Command (? for help): n
Partition Number: <default>
First sector: <default>
Last sector: <default>
Hex Code: <default>

Command (? for help): w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? (Y/N): Y
OK; writing new GUID partition table (GPT) to $DEVICE
The operation has completed successfully.
```

La chiffrer avec dm-crypt :

```console
$ cryptsetup -y luksFormat --label ARCH $ARCH_DEVICE
$ cryptsetup open $ARCH_DEVICE arch
```

Créer le volume et le groupe LVM :

```console
$ pvcreate /dev/mapper/arch
$ vgcreate arch /dev/mapper/arch
```

Afficher l'espace disque disponible sur le groupe LVM avec `vgdisplay`, puis créer le sous-volume.
Laisser un peu d'espace disponible pour pouvoir ultérieurement créer des snapshots.

```console
$ lvcreate -L 200G -n root arch
```

Formater le sous-volume au format ext4 :

```console
$ mkfs.ext4 -L ARCH_ROOT /dev/mapper/arch-root
```

## Montage des systèmes de fichier

Monter la hiérarchie nouvellement créée sur `/mnt` :

```console
$ mount /dev/mapper/arch-root /mnt
$ mkdir /mnt/boot /mnt/efi
$ mount $EFI_DEVICE /mnt/efi
$ mount $BOOT_DEVICE /mnt/boot
```
