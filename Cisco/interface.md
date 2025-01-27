## Configuration des interfaces du routeur

### Sur le routeur
1. Activez les privilèges d'administration :
   ```bash
   enable
   ```
2. Entrez en mode de configuration globale :
   ```bash
   conf t
   ```
3. Configurez une interface avec une adresse IP :
   ```bash
   interface g0/0
   ip address <adresse_ip> <masque>
   no shutdown
   exit
   ```
4. Configurez une autre interface si nécessaire :
   ```bash
   interface g0/1
   ip address <adresse_ip> <masque>
   no shutdown
   exit
   ```
5. Sauvegardez la configuration :
   ```bash
   do wr
   ```

### Exemple
Si vous configurez les interfaces avec les adresses `192.168.1.1/24` pour `g0/0` et `192.168.2.1/24` pour `g0/1` :
```bash
interface g0/0
ip address 192.168.1.1 255.255.255.0
no shutdown
exit

interface g0/1
ip address 192.168.2.1 255.255.255.0
no shutdown
exit
```

## Remarques sur les interfaces
- **Commandes importantes** :
  - `ip address` : Attribue une adresse IP et un masque de sous-réseau à l'interface.
  - `no shutdown` : Active l'interface (elle est désactivée par défaut).
- **Planification** : Assurez-vous que les adresses IP des interfaces correspondent au plan d'adressage du réseau.
