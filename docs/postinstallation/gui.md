# Interface graphique

## Polices de caractère

Installer quelques jeux de polices de caractère :

```console
$ pacman -S ttf-croscore ttf-dejavu noto-fonts noto-fonts-emoji ttf-roboto
```

## GNOME

Installer GNOME :

```console
$ pacman -S gnome
$ systemctl enable --now gdm.service
```

### Nemo

Installer Nemo :

```console
$ pacman -S nemo nemo-fileroller
```

Dans la session GNOME, le définir en tant qu'explorateur de fichiers par défaut :

```console
$ xdg-mime default nemo.desktop inode/directory application/x-gnome-saved-search
```

Si l'on souhaite afficher le bureau avec Nemo, exécuter les commandes suivantes :

```console
$ gsettings set org.nemo.desktop show-desktop-icons true
$ grep -v 'OnlyShowIn=X-Cinnamon;' /usr/share/applications/nemo-autostart.desktop > ~/.config/autostart/nemo-autostart.desktop
```

## Sway

Installer Wayland :

```console
$ pacman -S wayland wl-clipboard wireplumber pipewire-pulse
```

Installer Sway et un ensemble de programmes utiles sous Wayland :

```console
$ pacman -S sway swaylock swayidle alacritty kanshi wofi mako grim slurp gammastep waybar otf-font-awesome xdg-desktop-portal-wlr xorg-xwayland
```

Pour passer le clavier en AZERTY, ajouter l'entrée suivante dans `~/.config/sway/config` :

```
input "type:keyboard" {
    xkb_layout "fr"
    xkb_variant "oss"
    xkb_numlock "enabled"
}
```
