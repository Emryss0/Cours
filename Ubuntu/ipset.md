# Configuration d'une adresse IP statique sur Ubuntu

## Introduction
Sur Ubuntu, la configuration réseau est gérée via `netplan`, un système moderne de gestion des interfaces réseau. Cette documentation explique comment configurer une adresse IP statique sur une machine Ubuntu.

## 1. Vérifier le nom de l'interface réseau
Avant de modifier la configuration, identifiez le nom de l'interface réseau avec la commande :

```bash
ip a
```

Les interfaces courantes sont `eth0`, `ens33`, ou `enpXsY`.

## 2. Modifier la configuration Netplan

Les fichiers de configuration de Netplan se trouvent dans `/etc/netplan/`. Éditez le fichier existant (ex. `00-installer-config.yaml` ou similaire) :

```bash
sudo nano /etc/netplan/01-netcfg.yaml
```

Ajoutez ou modifiez la configuration suivante :

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses:
          - 8.8.8.8
          - 8.8.4.4
```

Explication des paramètres :
- `dhcp4: no` : Désactive DHCP pour utiliser une IP statique.
- `addresses` : Définit l'adresse IP et le masque de sous-réseau.
- `gateway4` : Définit la passerelle.
- `nameservers` : Spécifie les serveurs DNS.

## 3. Appliquer la configuration

Appliquez les changements avec la commande :

```bash
sudo netplan apply
```

## 4. Vérifier la configuration

Vérifiez que l'adresse IP est bien appliquée :

```bash
ip a show eth0
```

Testez la connectivité avec :

```bash
ping -c 4 8.8.8.8
```

## Conclusion
Vous avez maintenant configuré une adresse IP statique sur Ubuntu avec Netplan. Cette méthode est essentielle pour les serveurs et les configurations réseau avancées.