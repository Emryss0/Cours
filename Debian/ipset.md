# Configuration d'une adresse IP statique sur Debian

## Introduction
Sur Debian, la configuration réseau est généralement gérée via le fichier `/etc/network/interfaces`. Ce fichier permet de définir des adresses IP statiques ou dynamiques pour les interfaces réseau. Cette documentation explique comment configurer une adresse IP statique sur une machine Debian.

## Étapes de configuration

### 1. Vérifier le nom de l'interface réseau
Avant de modifier la configuration, il est important de connaître le nom de l'interface réseau. Utilisez la commande suivante pour lister les interfaces disponibles :

```bash
ip a
```

Les interfaces courantes sont souvent `eth0`, `ens33` ou `enpXsY` selon le matériel et la version de Debian.

### 2. Modifier le fichier `/etc/network/interfaces`

Ouvrez le fichier de configuration avec un éditeur de texte comme `nano` :

```bash
sudo nano /etc/network/interfaces
```

Ajoutez ou modifiez la section correspondante à votre interface réseau. Exemple pour une interface nommée `eth0` :

```plaintext
auto eth0
iface eth0 inet static
    address 192.168.1.100
    netmask 255.255.255.0
    gateway 192.168.1.1
    dns-nameservers 8.8.8.8 8.8.4.4
```

Explication des paramètres :
- `auto eth0` : Active l'interface au démarrage.
- `iface eth0 inet static` : Définit l'interface en mode statique.
- `address` : Adresse IP assignée à la machine.
- `netmask` : Masque de sous-réseau.
- `gateway` : Passerelle par défaut.
- `dns-nameservers` : Serveurs DNS à utiliser.

### 3. Redémarrer le service réseau

Une fois les modifications enregistrées, redémarrez le service réseau pour appliquer les changements :

```bash
sudo systemctl restart networking
```

Ou, si nécessaire, redémarrez la machine :

```bash
sudo reboot
```

### 4. Vérifier la configuration

Après le redémarrage, vérifiez que l'adresse IP a bien été appliquée avec :

```bash
ip a show eth0
```

Ou pour tester la connectivité :

```bash
ping -c 4 8.8.8.8
```

## Conclusion
Vous avez maintenant configuré une adresse IP statique sur Debian. Cette méthode est utile pour les serveurs ou les machines nécessitant une adresse IP fixe sur un réseau local.

