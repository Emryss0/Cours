# Configuration de HAProxy sur Debian

## Introduction
HAProxy est un logiciel open-source de répartition de charge (load balancer) et de proxy pour les protocoles TCP et HTTP. Cette documentation explique comment installer et configurer HAProxy sur Debian.

## 1. Installation de HAProxy

Mettez à jour la liste des paquets et installez HAProxy avec la commande suivante :

```bash
sudo apt update && sudo apt install -y haproxy
```

Vérifiez l'installation en consultant la version installée :

```bash
haproxy -v
```

## 2. Configuration de HAProxy

Le fichier principal de configuration se trouve dans `/etc/haproxy/haproxy.cfg`. Ouvrez-le avec un éditeur de texte :

```bash
sudo nano /etc/haproxy/haproxy.cfg
```

Ajoutez la configuration suivante pour mettre en place un équilibrage de charge entre plusieurs serveurs web :

```plaintext
global
    log /dev/log local0
    maxconn 4096

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend http_front
    bind *:80
    default_backend web_servers

backend web_servers
    balance roundrobin
    server web1 192.168.1.101:80 check
    server web2 192.168.1.102:80 check
```

Explication :
- `frontend http_front` : Définit l'entrée du trafic sur le port 80.
- `backend web_servers` : Contient les serveurs web vers lesquels le trafic sera distribué en mode `roundrobin`.
- `server web1/web2` : Définit les serveurs avec leur IP et un mécanisme de vérification (`check`).

## 3. Redémarrer et Activer HAProxy

Appliquez la nouvelle configuration en redémarrant HAProxy :

```bash
sudo systemctl restart haproxy
```

Activez HAProxy pour qu'il démarre automatiquement au boot :

```bash
sudo systemctl enable haproxy
```

## 4. Vérification du Fonctionnement

Testez si HAProxy écoute bien sur le port 80 :

```bash
sudo ss -tulnp | grep haproxy
```

Essayez d'accéder à HAProxy via un navigateur ou avec `curl` :

```bash
curl http://<IP_HAPROXY>
```

## Conclusion
Votre HAProxy est maintenant installé et configuré pour équilibrer la charge entre plusieurs serveurs web. Vous pouvez adapter la configuration selon vos besoins (ajout de SSL, gestion avancée du trafic, etc.).

