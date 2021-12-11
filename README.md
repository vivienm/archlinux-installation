# Guide d'installation Arch Linux

Ce document décrit les différentes étapes nécessaires à l'installation d'Arch Linux sur un ordinateur personnel.

Arch Linux est une distribution qui laisse une grande liberté de choix à ses utilisateurs.
La procédure d'installation décrite ici a les caractéristiques suivantes :

* Le système démarre en UEFI et utilise systemd-boot comme chargeur de démarrage.
* La partition principale est au format ext4 et se trouve dans un volume logique LVM, lui-même chiffré avec dm-crypt.
* La disposition clavier est en AZERTY.
* Il n'y a pas de partition swap, l'hibernation est impossible.
* mkinitcpio utilise des crochets systemd.
