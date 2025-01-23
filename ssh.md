# Configuration de SSH

## Sur le routeur
1. Définissez le nom de domaine :
   ```bash
   ip domain-name ipssi.com
   ```
2. Configurez un nom d'hôte pour le routeur :
   ```bash
   hostname Routeur1
   ```
3. Générez une clé RSA pour SSH :
   ```bash
   crypto key generate rsa
   ```
4. Créez un utilisateur avec un mot de passe :
   ```bash
   username broly password ipssi
   ```
5. Configurez les lignes virtuelles pour utiliser SSH :
   ```bash
   line vty 0 4
   login local
   transport input ssh
   ```

## Sur le PC
1. Ouvrez une session SSH avec l'utilisateur et l'adresse IP du routeur :
   ```bash
   ssh -l broly <adresse_ip_du_routeur>
   ```

### Exemple
Si l'adresse IP de votre routeur est `192.168.1.1`, la commande sera :
```bash
ssh -l broly 192.168.1.1
```

## Remarques
- Assurez-vous que le protocole SSH est activé sur le routeur.
- La génération de clé RSA nécessite une longueur de clé. Si elle n'est pas précisée, le routeur demandera de la configurer (ex. 1024 bits ou 2048 bits).
- Si votre PC ne dispose pas d'un client SSH, vous pouvez en installer un, comme **PuTTY** ou utiliser une application intégrée comme `ssh` sous Linux ou macOS.
