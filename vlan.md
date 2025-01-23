## Configuration des VLANs

### Sur le switch
1. Activez les privilèges d'administration :
   ```bash
   enable
   ```
2. Entrez en mode de configuration globale :
   ```bash
   conf t
   ```
3. Créez un VLAN et donnez-lui un nom :
   ```bash
   vlan 10
   name vlan10
   exit
   ```
4. Configurez un port en mode access pour le VLAN :
   ```bash
   interface f0/1
   switchport mode access
   switchport access vlan 10
   exit
   ```
5. Configurez un port en mode trunk :
   ```bash
   interface f0/2
   switchport mode trunk
   exit
   ```

### Sur le routeur
1. Activez les privilèges d'administration :
   ```bash
   enable
   ```
2. Entrez en mode de configuration globale :
   ```bash
   conf t
   ```
3. Configurez une sous-interface pour le VLAN :
   ```bash
   interface g0/0.10
   encapsulation dot1Q 10
   ip address <adresse_ip_du_vlan> <masque>
   exit
   ```
4. Sauvegardez la configuration :
   ```bash
   do wr
   ```

### Exemple
Si le VLAN 10 a l'adresse IP `192.168.10.1` avec le masque `255.255.255.0`, la configuration de la sous-interface sera :
```bash
interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.10.1 255.255.255.0
exit
```

## Remarques sur les modes VLAN
- **Mode Access** : Ce mode associe un port à un seul VLAN spécifique. Il est utilisé pour connecter des périphériques finaux comme des PC ou des imprimantes.
- **Mode Trunk** : Ce mode permet à un port de transporter plusieurs VLANs en encapsulant les trames avec un identifiant VLAN (802.1Q). Il est utilisé pour connecter des switches entre eux ou un switch à un routeur.
