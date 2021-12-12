# Démarrage

Démarrer l'ordinateur et se connecter avec le compte `root`.

## Connexion internet

Démarrer les services réseau :

```console
$ systemctl enable --now systemd-networkd.service systemd-resolved.service
```

Si l'ordinateur ne dispose pas d'une liaison Ethernet, démarrer également le service `iwd` :

```console
$ systemctl enable --now iwd.service
```

Suivre ensuite [les instructions indiquées précédemment](../installation/preinstallation.md#connexion-internet) pour établir une connexion réseau.
