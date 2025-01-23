
## Configuration RIP

### Sur le routeur
1. Activez les privilèges d'administration :
   ```bash
   enable
   ```
2. Entrez en mode de configuration globale :
   ```bash
   conf t
   ```
3. Activez le protocole RIP :
   ```bash
   router rip
   ```
4. Spécifiez la version RIP (version 2 est recommandée) :
   ```bash
   version 2
   ```
5. Déclarez les réseaux à inclure dans le routage RIP :
   ```bash
   network <adresse_ip_du_reseau_1>
   network <adresse_ip_du_reseau_2>
   ```
6. Quittez le mode de configuration RIP :
   ```bash
   exit
   ```
7. Sauvegardez la configuration :
   ```bash
   do wr
   ```

### Exemple
Si les réseaux `192.168.1.0/24` et `192.168.2.0/24` doivent être inclus :
```bash
router rip
version 2
network 192.168.1.0
network 192.168.2.0
exit
```

## Remarques sur RIP
- **Version 1** : Utilise des annonces de routage en broadcast, ne prend pas en charge les sous-réseaux.
- **Version 2** : Utilise des annonces en multicast et prend en charge les sous-réseaux, recommandé pour les réseaux modernes.
