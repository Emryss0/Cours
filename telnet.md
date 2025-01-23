# Configuration de Telnet

## Sur le routeur
1. Activez les privilèges d'administration :
   ```bash
   enable
   ```
2. Entrez en mode de configuration globale :
   ```bash
   conf t
   ```
3. Configurez les lignes virtuelles pour Telnet :
   ```bash
   line vty 0 4
   ```
4. Définissez un mot de passe pour les sessions Telnet :
   ```bash
   password cisco
   ```
5. Activez l'authentification par mot de passe :
   ```bash
   login
   ```
6. Quittez le mode de configuration des lignes :
   ```bash
   exit
   ```

## Sur le PC
1. Ouvrez une session Telnet avec l'adresse IP du routeur :
   ```bash
   telnet <adresse_ip_du_routeur>
   ```

### Exemple
Si l'adresse IP de votre routeur est `192.168.1.1`, la commande sera :
```bash
telnet 192.168.1.1
```

## Remarques
- Assurez-vous que Telnet est activé sur votre routeur et que l'interface réseau est correctement configurée.
- Si vous utilisez une version récente de Windows, Telnet n'est pas activé par défaut. Vous pouvez l'activer via :
  - **Panneau de configuration > Programmes > Activer ou désactiver des fonctionnalités Windows > Client Telnet**.
