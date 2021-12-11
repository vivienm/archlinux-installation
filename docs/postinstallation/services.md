# Services système

## Docker

Installer et démarrer Docker :

```console
$ pacman -S docker
$ systemctl enable --now docker.service
$ usermod -aG docker vivien
```

## CUPS

Installer et démarrer CUPS :

```console
$ pacman -S cups
$ systemctl enable --now cups.service
```

## Tailscale

Installer et démarrer TailScale :

```console
$ pacman -S tailscale
$ systemctl enable --now tailscaled.service
```

Authentifier la machine avec `tailscale up`.
