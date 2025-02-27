# Guide des Bases du Scripting Bash

## Introduction
Le scripting Bash permet d'automatiser des tâches sous Linux en enchaînant des commandes dans un fichier exécutable. Ce guide couvre les bases du scripting Bash, en utilisant des exemples pratiques.

---

## 1. Structure de Base d'un Script Bash
Un script Bash commence toujours par le **shebang** :

```bash
#!/bin/bash
```

Cela indique que le script doit être interprété par Bash.

**Exemple :** Afficher la date actuelle

```bash
#!/bin/bash
date +"%Y %m %d"
```

Exécutez ce script après lui avoir donné les permissions d'exécution :

```bash
chmod +x script.sh
./script.sh
```

---

## 2. Variables et Lecture d'Entrées

### Déclaration et Utilisation de Variables
```bash
#!/bin/bash
nom="Emrys"
echo "Bonjour, $nom !"
```

### Lecture d'une Entrée Utilisateur
```bash
#!/bin/bash
echo "Entrez un nombre :"
read nombre
if [ $nombre -eq 1 ]; then
    echo "Est égal à 1"
elif [ $nombre -gt 1 ]; then
    echo "Est supérieur à 1"
else
    echo "Est inférieur à 1"
fi
```

---

## 3. Arguments en Ligne de Commande

Bash permet de passer des arguments à un script :

```bash
#!/bin/bash
echo "Premier argument : $1"
echo "Nombre total d'arguments : $#"
echo "Liste des arguments : $@"
```

Exécuter le script avec des arguments :
```bash
./script.sh arg1 arg2 arg3
```

---

## 4. Boucles en Bash

### Boucle `for`
```bash
#!/bin/bash
for i in 1 2 3 4 5; do
    echo "Itération : $i"
done
```

### Boucle `while`
```bash
#!/bin/bash
compteur=1
while [ $compteur -le 5 ]; do
    echo "Compteur : $compteur"
    ((compteur++))
done
```

---

## 5. Vérification d'Existence d'un Fichier

```bash
#!/bin/bash
echo -n "Entrez le nom du fichier : "
read fichier
if [ -f "$fichier" ]; then
    echo "Le fichier '$fichier' existe."
else
    echo "Le fichier '$fichier' n'existe pas."
fi
```

---

## 6. Gestion des Processus et Codes de Retour

### Vérifier si une commande réussit
```bash
#!/bin/bash
ls /var/www/html
if [ $? -eq 2 ]; then
    mkdir -p /var/www/html
fi
```

### Vérifier un processus actif
```bash
#!/bin/bash
process="nginx"
if pgrep -x "$process" > /dev/null; then
    echo "$process est en cours d'exécution."
else
    echo "$process est arrêté. Redémarrage..."
    sudo systemctl start "$process"
fi
```

---

## 7. Déploiement Automatisé de Conteneurs LXC avec HAProxy et Nginx

Ce script automatise le déploiement d'un serveur HAProxy et de plusieurs serveurs Nginx dans des conteneurs LXC sur Proxmox.

```bash
#!/bin/bash
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
fi
```

---

## 8. Explication des Commandes et Opérateurs en Bash

### Opérateurs de Comparaison
- `-eq` : Vérifie l'égalité entre deux nombres. (`[ "$a" -eq "$b" ]`)
- `-ne` : Vérifie si deux nombres sont différents.
- `-lt` : Inférieur à.
- `-le` : Inférieur ou égal à.
- `-gt` : Supérieur à.
- `-ge` : Supérieur ou égal à.

### Vérification des Fichiers
- `-f` : Vérifie si un fichier existe et est un fichier ordinaire.
- `-d` : Vérifie si un répertoire existe.
- `-e` : Vérifie si un fichier ou un répertoire existe.

### Lecture et Affichage
- `read` : Lit une entrée utilisateur.
- `read -p "Message" variable` : Affiche un message avant de lire l'entrée.

### Variables Spéciales
- `$?` : Code de retour de la dernière commande exécutée.
- `$#` : Nombre d'arguments passés au script.
- `$@` : Liste des arguments passés au script.

---

## 9. Fonctions Bash Courantes

### Définition d'une fonction simple
```bash
#!/bin/bash
ma_fonction() {
    echo "Ceci est une fonction Bash."
}
ma_fonction
```

### Fonction avec paramètres
```bash
#!/bin/bash
addition() {
    echo "Résultat : $(( $1 + $2 ))"
}
addition 3 5
```

### Fonction avec retour de valeur
```bash
#!/bin/bash
carre() {
    echo $(( $1 * $1 ))
}
resultat=$(carre 4)
echo "Le carré de 4 est : $resultat"
```

### Fonction vérifiant l'existence d'un fichier
```bash
#!/bin/bash
fichier_existe() {
    if [ -f "$1" ]; then
        echo "Le fichier $1 existe."
    else
        echo "Le fichier $1 n'existe pas."
    fi
}
fichier_existe /etc/passwd
```

---

## Conclusion

Ce guide couvre les bases du scripting Bash avec des exemples pratiques allant de la gestion des fichiers à l'automatisation des conteneurs. Expérimentez et adaptez ces scripts selon vos besoins !

