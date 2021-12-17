# Services système

## Docker

Installer et démarrer Docker :

```console
$ pacman -S docker
$ systemctl enable --now docker.service
```

Ajouter éventuellement l'administrateur au groupe `docker` :

```console
$ usermod -aG docker vivien
```

## CUPS

Installer et démarrer CUPS :

```console
$ pacman -S cups
$ systemctl enable --now cups.service
```

## Tailscale

Installer et démarrer Tailscale :

```console
$ pacman -S tailscale
$ systemctl enable --now tailscaled.service
```

Authentifier la machine avec `tailscale up`.
