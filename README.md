# Installation de Arch Linux

## Installation du base system

Download iso à partir de : https://www.archlinux.org/download/ 

Booter sur l'iso à partir d'une clé USB

### Configurer le clavier en FR
```
loadkeys fr
```

### Configurer le reseau
Si vous etes en wifi lancer la commande suivante :
```
wifi-menu
```

Tester la connection reseau
```
ping google.com
```

### Configuration des Disques
Utiliser cfdisk ou fdisk
```
/dev/sda1 /boot 1024 Mo ext4
/dev/sda2 XXX swap
/dev/sda3 / 20Go (minimum) ext4
/dev/sda4 /home xxxGo ext4
```

Formater les partitions
```
mkfs.ext4 /dev/sda1
mkfs.ext4 /dev/sda3
mkfs.ext4 /dev/sda4
```

La partition de swap :
```
mkswap /dev/sda2
swapon /dev/sda2
```

Monter les partitions pour l'installation
```
mount /dev/sda3 /mnt
mkdir /mnt/{boot,home}
mount /dev/sda1 /mnt/boot
mount /dev/sda4 /mnt/home
```

### Installation du système 
```
pacstrap  /mnt base 
```

### Configuration du système
Générer le /etc/fstab
```
genfstab -U -p /mnt >> /mnt/etc/fstab
```



Chrooter dans le nouveau système :
```
arch-chroot /mnt
```

Renseignez le nom de la machine dans le fichier /etc/hostname :
```
echo NomDeLaMachine > /etc/hostname
```

Renseignez le nom de la machine dans le fichier /etc/hosts :
```
echo '127.0.1.1 NomDeLaMachine.localdomain NomDeLaMachine' >> /etc/hosts
```
Créez un lien symbolique /etc/localtime afin de choisir votre fuseau horaire, par exemple pour la France :
```
ln -sf /usr/share/zoneinfo/Europe/Paris /etc/localtime
```

Éditez le fichier /etc/locale.gen et décommentez votre locale et en_US.UTF-8 UTF-8. puis exécutez la commande suivante :

```
locale-gen
```

Ajoutez le nom de la locale au fichier /etc/locale.conf, par exemple pour le français en UTF-8 :
```
echo LANG="fr_FR.UTF-8" > /etc/locale.conf
echo LC_COLLATE="C" > /etc/locale.conf
```

Vous pouvez spécifier la locale pour la session courante (ça évitera des messages d'alerte par la suite) avec la commande :
```
export LANG=fr_FR.UTF-8
```

Éditez le fichier /etc/vconsole.conf afin d'y spécifier la disposition du clavier que vous souhaitez utiliser :
```
echo KEYMAP=fr-latin9 > /etc/vconsole.conf
echo FONT=lat9w-16 >> /etc/vconsole.conf
```

Définissez un mot passe pour le root :
```
passwd
```

Créer un utilisateur
```
useradd -m -g users -G wheel,storage,power -s /bin/bash nom-de-l’utilisateur
passwd nom-de-l’utilisateur
```

Installation de NetworkManager (Gnome Prereq)
```
pacman -Syy networkmanager
systemctl enable NetworkManager
```

Installer les paquets pour le wifi :
```
pacman -S iw wpa_supplicant dialog wpa-actiond
```

### Installation et configuration de grub
Il vous faut installer le paquet grub et os-prober :
```
pacman -S grub os-prober
```

Pour l'installer sur le disque /dev/sda :
```
grub-install --target=i386-pc --no-floppy --recheck /dev/sda
```

Générer le fichier de configuration principal
```
 grub-mkconfig -o /boot/grub/grub.cfg
```


### Umount et reboot 
L'installation est fini et faut quitter le chroot, démonter les partitions et rebooter le système
```
exit
umount -R /mnt
umount -R /mnt/xxx
reboot
```
## Post Installation
