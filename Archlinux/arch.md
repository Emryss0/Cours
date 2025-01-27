# Installation d'Arch Linux

## Téléchargement de l'ISO
Commencez par télécharger l'ISO d'installation d'Arch Linux à l'adresse suivante : [https://archlinux.org/download/](https://archlinux.org/download/).

Une fois l'ISO téléchargée, sélectionnez le type Linux et choisissez la version Arch Linux.

**Remarque :** Il est nécessaire d'ajouter deux disques :
- Un disque principal de 20 Go.
- Un second disque de taille moindre.

## Connexion à la machine
Vous pouvez établir une connexion SSH avec la machine nouvellement créée ou exécuter directement les commandes dans son terminal.

## Partitionnement avec `fdisk`
Utilisez la commande `fdisk` pour partitionner le disque `sda`.

### Création des partitions
1. **Partition primaire :**
   - `sda1` : 1 Go
   - `sda2` : 512 Mo
   - `sda3` : 2 Go
2. **Partition étendue :**
   - Ajoutez trois partitions dans l'espace étendu : `sda5`, `sda6`, et `sda7`.

![Partitionnement avec fdisk](images/image1.png)

## Formatage des partitions
Utilisez les commandes suivantes pour formater les partitions :

```bash
mkfs.ext4 /dev/sda1
mkfs.fat -F 32 /dev/sda2
mkswap /dev/sda3
mkfs.ext4 /dev/sda5
mkfs.ext4 /dev/sda6
mkfs.ext4 /dev/sda7
```

### Explications des formats :
- **`ext4` :** Format robuste et performant.
- **`swap` :** Étend la mémoire vive et permet l'hibernation.
- **`fat32` :** Compatible avec UEFI.

![Formatage des partitions](images/image2.png)

## Montage des partitions
Montez les partitions avec les commandes suivantes :

```bash
mount /dev/sda1 /mnt
mkdir -p /mnt/boot /mnt/var /mnt/usr /mnt/home
mount /dev/sda2 /mnt/boot -o nosuid,nodev,noexec
mount /dev/sda5 /mnt/var -o nosuid,nodev,noexec
mount /dev/sda6 /mnt/usr -o nodev
mount /dev/sda7 /mnt/home -o nosuid,nodev,noexec
```

![Montage des partitions](images/image3.png)

## Installation de base
Installez les paquets essentiels avec `pacstrap` :

```bash
pacstrap /mnt base linux linux-firmware
```

Générez ensuite le fichier `/etc/fstab` :

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

Ce fichier automatise le montage des partitions au démarrage.

## Configuration du système

### Définir le fuseau horaire
1. Redémarrez et entrez dans la racine de la machine.
2. Définissez la timezone :

```bash
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime
hwclock --systohc
```

### Configuration des locales
1. Installez `vim` :

```bash
pacman -S vim
```

2. Modifiez le fichier `locale.gen` :
   - Retirez le commentaire de la ligne `fr_FR.UTF-8`.
   - Exécutez :

   ```bash
   locale-gen
   ```

3. Ajoutez les configurations suivantes :
   - Dans `/etc/locale.conf` :

   ```bash
   LANG=fr_FR.UTF-8
   ```

   - Dans `/etc/vconsole.conf` :

   ```bash
   KEYMAP=fr
   ```

### Définir le nom de l'hôte et le mot de passe root
1. Configurez le fichier hostname :

```bash
echo "hostname" > /etc/hostname
```

2. Définissez le mot de passe root :

```bash
passwd
```

## Configuration du bootloader
1. Ajoutez un hook `usr` dans `/etc/mkinitcpio.conf` pour permettre au système de trouver les fichiers nécessaires au démarrage :

```bash
HOOKS=(base udev autodetect modconf block filesystems usr fsck)
```

2. Générez l'initramfs :

```bash
mkinitcpio -P
```

### Installation de GRUB
1. Installez GRUB :

```bash
pacman -S grub
```

2. Installez GRUB sur le disque principal :

```bash
grub-install --target=i386-pc --no-floppy --recheck /dev/sda
```

3. Générez le fichier de configuration :

```bash
grub-mkconfig -o /boot/grub/grub.cfg
```

![Configuration de GRUB](images/image4.png)

Déconnectez l'ISO d'installation et redémarrez. Vous avez maintenant un système Arch Linux fonctionnel.
