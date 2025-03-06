# Configuration d'une adresse IP statique sur Arch Linux

## 1. Vérifier les interfaces réseau

Avant de configurer une adresse IP, il est important d’identifier l’interface réseau :

```sh
ip addr show
```

Recherchez le nom de votre interface réseau, par exemple `eth0` ou `enp3s0`.

## 2. Configurer une adresse IP statique temporairement

Si vous souhaitez définir une adresse IP temporairement (jusqu’au prochain redémarrage) :

```sh
ip addr add 192.168.1.100/24 dev eth0
ip route add default via 192.168.1.1
```

Remplacez `192.168.1.100/24` par l’IP souhaitée et `192.168.1.1` par l’IP de votre passerelle.

## 3. Configurer une adresse IP statique de manière permanente

### a. Via systemd-networkd (Recommandé)

Assurez-vous que `systemd-networkd` est activé :

```sh
sudo systemctl enable --now systemd-networkd
```

Créez un fichier de configuration pour l’interface :

```sh
sudo nano /etc/systemd/network/20-static.network
```

Ajoutez les lignes suivantes en remplaçant les valeurs selon votre configuration :

```ini
[Match]
Name=eth0

[Network]
Address=192.168.1.100/24
Gateway=192.168.1.1
DNS=8.8.8.8
```

Redémarrez le service :

```sh
sudo systemctl restart systemd-networkd
```

### b. Via Netplan (Si installé)

Modifiez ou créez un fichier de configuration :

```sh
sudo nano /etc/netplan/01-network-manager-all.yaml
```

Ajoutez :

```yaml
network:
  version: 2
  ethernets:
    eth0:
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
```

Appliquez la configuration :

```sh
sudo netplan apply
```

## 4. Vérification

Pour confirmer que l’IP est bien configurée :

```sh
ip addr show eth0
ip route
```

## 5. Redémarrer pour appliquer les changements

Si nécessaire, redémarrez le système :

```sh
sudo reboot
```

---

Votre Arch Linux devrait maintenant être configuré avec une adresse IP statique ! 🚀