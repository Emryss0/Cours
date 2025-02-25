# Guide du Scripting Bash

## Introduction
Bash (Bourne Again SHell) est l'un des interpréteurs de commandes les plus utilisés sous Linux. Il permet d'automatiser des tâches grâce à des scripts, facilitant ainsi l'administration système et le traitement des données.

---

## 1. Affichage de la date
Pour afficher la date et l'heure dans un format personnalisé :
```bash
date +"%Y %m %d"
```
Explication :
- `%Y` : Année (ex. 2025)
- `%m` : Mois (ex. 02 pour février)
- `%d` : Jour du mois (ex. 25)

---

## 2. Gestion des adresses IP
Obtenir l'adresse IPv4 d'une interface réseau :
```bash
ip=$(ip -4 -o address show <interface> | awk '{print $4}' | awk -F '/' '{print $1}')
```
Explication :
- `ip -4 -o address show <interface>` : Affiche les adresses IPv4 de l'interface spécifiée.
- `awk '{print $4}'` : Extrait le 4e champ (contenant l'adresse IP et le masque).
- `awk -F '/' '{print $1}'` : Supprime le masque de sous-réseau, ne conservant que l'IP.

Remplace `<interface>` par le nom réel de l'interface (ex: `eth0`, `wlan0`, `vmbr0`).

---

## 3. Boucles en Bash
### Boucle `for` avec une liste statique
```bash
for i in 1 2 3 4 5 6; do
    echo $i
done
```

### Boucle `for` avec une séquence
```bash
for i in {1..255}; do
    echo "Adresse IP : 192.168.1.$i"
done
```

### Boucle `for` avec `ping`
Effectuer un `ping` sur une plage d’adresses IP :
```bash
for i in {1..255}; do
    ping -c 1 -W 1 192.168.1.$i | grep ttl &
done
```
Explication :
- `ping -c 1` : Envoie un seul paquet ICMP.
- `-W 1` : Timeout d’une seconde.
- `grep ttl` : Filtre les réponses valides.
- `&` : Lance chaque `ping` en arrière-plan pour accélérer l'exécution.

---

## 4. Lecture de l'entrée utilisateur
```bash
echo "Entrer un nombre:"
read toto
if [ "$toto" -eq 1 ]; then
    echo "Est égal à 1"
elif [ "$toto" -gt 1 ]; then
    echo "Est supérieur à 1"
else
    echo "Est inférieur à 1"
fi
```
Explication :
- `read toto` : Stocke l’entrée utilisateur dans la variable `toto`.
- `-eq`, `-gt`, `-lt` : Opérateurs de comparaison numériques en Bash.
- `if [ condition ]; then ... fi` : Structure conditionnelle standard.

Autres opérateurs de comparaison :
| Opérateur | Signification |
|-----------|--------------|
| `-lt`     | Moins que    |
| `-le`     | Inférieur ou égal |
| `-gt`     | Plus grand que |
| `-ge`     | Supérieur ou égal |
| `-ne`     | Différent de |
| `-eq`     | Égal à |

---

## 5. Gestion des arguments
Un script Bash peut recevoir des arguments depuis la ligne de commande :
```bash
#!/bin/bash

echo "Premier argument : $1"
echo "Nombre total d'arguments : $#"
echo "Liste des arguments : $@"
```
Explication :
- `$1` : Premier argument.
- `$#` : Nombre total d’arguments.
- `$@` : Liste complète des arguments.

---

## 6. Vérification des erreurs et codes de retour
Chaque commande Bash retourne un code de sortie (`$?`) indiquant son état d'exécution :
```bash
ls /chemin/inexistant
if [ $? -eq 2 ]; then
    echo "Le dossier n'existe pas, création en cours..."
    mkdir -p /chemin/inexistant
fi
```
Explication :
- `$?` stocke le code de sortie de la dernière commande exécutée.
- `-eq 2` : Vérifie si l'erreur correspond à "fichier ou répertoire inexistant".
- `mkdir -p` : Crée le répertoire s’il n'existe pas déjà.

---

## 7. Codes de sortie personnalisés
Un script peut explicitement définir son code de sortie :
```bash
echo "Script terminé"
exit 127
```
Explication :
- `exit 127` : Définit un code de sortie personnalisé.
- Une valeur différente de `0` indique généralement une erreur.

---

## Conclusion
Ce guide couvre les bases du scripting Bash, notamment :
- Affichage de la date et récupération d'adresses IP.
- Utilisation des boucles et des conditions.
- Lecture de l'entrée utilisateur et gestion des arguments.
- Gestion des erreurs et codes de sortie.

Le scripting Bash est un outil puissant pour automatiser des tâches sous Linux. En combinant ces concepts, vous pouvez écrire des scripts robustes et efficaces ! 🚀
