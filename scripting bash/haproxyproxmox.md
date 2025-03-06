# Documentation du Script de Déploiement HAProxy et Serveur Web

## Description
Ce script Bash automatise le déploiement d'un serveur HAProxy ainsi que d'un ou plusieurs serveurs web sous Proxmox en utilisant des conteneurs LXC. Il permet :

- De vérifier si un conteneur HAProxy existe et, si ce n'est pas le cas, d'en créer un avec la configuration nécessaire.
- De déployer dynamiquement des serveurs web supplémentaires avec Nginx.
- D'ajouter chaque nouveau serveur web à la configuration de HAProxy pour assurer un équilibrage de charge en mode `roundrobin`.
- De personnaliser la page d'accueil de chaque serveur web avec son identifiant.

## Fonctionnement
1. Vérifie si un conteneur HAProxy est déjà présent. Si non, il le crée et installe HAProxy.
2. Recherche le premier ID de conteneur disponible pour un nouveau serveur web.
3. Crée un conteneur basé sur une image Ubuntu, installe et configure Nginx.
4. Modifie la configuration de HAProxy pour inclure le nouveau serveur web.
5. Redémarre HAProxy pour appliquer les changements.

## Script
```bash
#!/bin/bash
# Urbain TOWANOU & Emrys AGBOTON
# Configuration des IPs et ID de base
HAPROXY_ID=101
HAPROXY_IP="192.168.2.132"
WEB_ID_BASE=102
WEB_IP_BASE="192.168.2.230"
GATEWAY="192.168.2.2"
STORAGE="local-lvm"
TEMPLATE="local:vztmpl/ubuntu-22.04-standard_22.04-1_amd64.tar.zst"
PASSWORD="password"

# Vérifier si HAProxy est déjà déployé
if ! pct list | grep -q "^$HAPROXY_ID "; then
    echo "Création du conteneur HAProxy ($HAPROXY_ID) avec IP $HAPROXY_IP..."
    pct create $HAPROXY_ID $TEMPLATE --hostname haproxy \
        --storage $STORAGE --password $PASSWORD --rootfs 2 \
        --cpuunits 1 --memory 512 \
        --net0 name=eth0,ip=$HAPROXY_IP/24,gw=$GATEWAY,bridge=vmbr0
    pct start $HAPROXY_ID
    sleep 10

    echo "Installation de HAProxy..."
    pct exec $HAPROXY_ID -- apt update
    pct exec $HAPROXY_ID -- apt install -y haproxy

    echo "Configuration de HAProxy..."
    pct exec $HAPROXY_ID -- bash -c "cat > /etc/haproxy/haproxy.cfg <<EOF
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
EOF"
    pct exec $HAPROXY_ID -- systemctl restart haproxy
fi

# Trouver le prochain ID disponible pour un serveur web
NEXT_ID=$WEB_ID_BASE
while pct list | grep -q "^$NEXT_ID "; do
    NEXT_ID=$((NEXT_ID + 1))
done

# Calculer l'IP correspondante
OFFSET=$((NEXT_ID - WEB_ID_BASE))
NEXT_IP="192.168.2.$((230 + OFFSET))"

echo "Création du serveur web ($NEXT_ID) avec IP $NEXT_IP..."
pct create $NEXT_ID $TEMPLATE --hostname web$OFFSET \
    --storage $STORAGE --password $PASSWORD --rootfs 2 \
    --cpuunits 1 --memory 512 \
    --net0 name=eth0,ip=$NEXT_IP/24,gw=$GATEWAY,bridge=vmbr0
pct start $NEXT_ID
sleep 10

echo "Installation de Nginx sur le serveur web ($NEXT_ID)..."
pct exec $NEXT_ID -- apt update
pct exec $NEXT_ID -- apt install -y nginx
pct exec $NEXT_ID -- systemctl enable --now nginx

# Modifier la page d'accueil de Nginx pour afficher l'offset
echo "Personnalisation de la page d'accueil Nginx..."
pct exec $NEXT_ID -- bash -c "echo '<h1>Bienvenue sur le serveur web</h1><p>Web: $OFFSET</p>' | tee /var/www/html/index.nginx-debian.html"
pct exec $NEXT_ID -- systemctl restart nginx

# Ajouter le serveur web à HAProxy
echo "Mise à jour de la configuration HAProxy avec le serveur $NEXT_IP..."
pct exec $HAPROXY_ID -- bash -c "echo '    server web$OFFSET $NEXT_IP:80 check' >> /etc/haproxy/haproxy.cfg"
pct exec $HAPROXY_ID -- systemctl restart haproxy

echo "Déploiement terminé ! HAProxy et le serveur web sont opérationnels."
echo "Accède à HAProxy via : http://$HAPROXY_IP"
```

