# Administration système

## Sudo

Installer sudo :

```console
$ pacman -S sudo
```

Créer le fichier `/etc/sudoers.d/wheel` avec pour contenu :

```
%wheel ALL=(ALL) ALL
```

## Shell

Installer Zsh :

```console
$ pacman -S zsh grml-zsh-config
$ chsh -s /usr/bin/zsh
$ exec zsh
```

## Administrateur système

Créer le ou les comptes administrateurs :

```console
$ groupadd -g 1000 vivien
$ useradd -u 1000 -g 1000 -G network,users,sys,wheel -m -s /usr/bin/zsh -c 'Vivien Maisonneuve' vivien
$ passwd vivien
```

## OpenSSH

Installer et démarrer OpenSSH :

```console
$ pacman -S openssh
$ systemctl enable --now sshd.service
```

Configurer les clés SSH autorisées (par exemple avec `ssh-copy-id`, ou en créant le fichier `~/.ssh/authorized_keys`).
Éditer ensuite le fichier `/etc/ssh/sshd_config` de manière à désactiver l'authentification par mot de passe :

```
PasswordAuthentication no
```

Redémarrer ensuite le service OpenSSH :

```console
$ systemctl restart sshd.service
```

## NTP

Démarrer le service NTP :

```console
$ systemctl enable --now systemd-timesyncd.service
```

## Gestionnaire de paquets

### Pacman

Éditer le fichier `/etc/pacman.conf` et décommenter la ligne

```
Color
```

Éditer le fichier `/etc/pacman.d/mirrorlist` de manière à avoir les entrées suivantes :

```
Server = https://mir.archlinux.fr/$repo/os/$arch
Server = https://mirrors.kernel.org/archlinux/$repo/os/$arch
Server = https://mirror.pkgbuild.com/$repo/os/$arch
```

Installer le paquet `pacman-contrib` et activer `paccache.timer` :

```console
$ pacman -S pacman-contrib
$ systemctl enable paccache.timer
```

Installer le paquet `pkgfile` et activer son timer :

```console
$ pacman -S pkgfile
$ systemctl enable pkgfile-update.timer
```

### Yay

Installer Yay :

```console
$ pacman -S --needed git base-devel
$ su vivien
$ cd
$ git clone https://aur.archlinux.org/yay-bin.git
$ cd yay-bin
$ makepkg -si
$ cd ..
$ rm -fr yay-bin
$ exit
```
