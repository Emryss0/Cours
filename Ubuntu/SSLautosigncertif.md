# Comment créer un certificat SSL sur Apache pour Ubuntu

## Introduction

TLS (Transport Layer Security) et son prédécesseur SSL (Secure Sockets Layer) sont des protocoles sécurisés permettant d'encapsuler le trafic normal dans une couche chiffrée.

Ces protocoles garantissent que les échanges entre des parties distantes restent protégés et inaccessibles à toute interception. Ils servent également à valider l'identité des domaines et des serveurs sur Internet via une autorité de certification (CA).

Dans ce guide, nous allons voir comment créer un certificat SSL auto-signé pour Apache sur un serveur Ubuntu, permettant ainsi de chiffrer le trafic. Ce type de certificat ne valide pas l'identité du serveur auprès d'une autorité tierce, mais il est suffisant pour sécuriser les échanges.

> **Remarque** : Il est recommandé d'utiliser **Let's Encrypt** plutôt qu'un certificat auto-signé. Let's Encrypt fournit gratuitement des certificats SSL/TLS reconnus par la majorité des navigateurs. Consultez le tutoriel dédié pour en savoir plus.

## Prérequis

Avant de commencer, assurez-vous de remplir les conditions suivantes :

- Vous devez avoir un utilisateur avec des privilèges `sudo`.
- Apache doit être installé sur votre serveur. Si ce n'est pas le cas, installez-le avec :
  
  ```bash
  sudo apt update
  sudo apt install apache2
  ```

## Étape 1 — Activer le module SSL

Le support SSL est inclus par défaut dans Apache sous Ubuntu. Il suffit de l'activer :

```bash
sudo a2enmod ssl
```

Redémarrez ensuite Apache pour appliquer la modification :

```bash
sudo service apache2 restart
```

## Étape 2 — Générer un certificat SSL auto-signé

Créez un répertoire dédié aux fichiers de certificat :

```bash
sudo mkdir /etc/apache2/ssl
```

Générez ensuite une clé privée et un certificat auto-signé avec la commande suivante :

```bash
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/apache2/ssl/apache.key \
  -out /etc/apache2/ssl/apache.crt
```

Explication des options :
- `-x509` : Génère un certificat auto-signé.
- `-nodes` : Ne protège pas la clé privée par un mot de passe (nécessaire pour le redémarrage automatique d'Apache).
- `-days 365` : Définit une durée de validité de 1 an.
- `-newkey rsa:2048` : Crée une clé privée RSA de 2048 bits en même temps que le certificat.
- `-keyout` : Spécifie l'emplacement du fichier de clé privée.
- `-out` : Spécifie l'emplacement du fichier du certificat.

Lors de l'exécution, OpenSSL demandera plusieurs informations, notamment le "Common Name" où vous devez entrer le nom de domaine ou l'adresse IP du serveur.

## Étape 3 — Configurer Apache pour utiliser SSL

Modifiez le fichier de configuration SSL par défaut d’Apache :

```bash
sudo nano /etc/apache2/sites-available/default-ssl.conf
```

Trouvez et modifiez les lignes suivantes :

```apache
ServerAdmin admin@example.com
ServerName your_domain.com
ServerAlias www.your_domain.com
DocumentRoot /var/www/html
SSLEngine on
SSLCertificateFile /etc/apache2/ssl/apache.crt
SSLCertificateKeyFile /etc/apache2/ssl/apache.key
```

Enregistrez et fermez le fichier.

## Étape 4 — Activer le site SSL et redémarrer Apache

Activez la configuration SSL :

```bash
sudo a2ensite default-ssl.conf
```

Redémarrez Apache pour appliquer les modifications :

```bash
sudo service apache2 restart
```

## Étape 5 — Tester la configuration

Accédez à votre site en utilisant HTTPS :

```text
https://your_domain.com
```

Vous verrez probablement un avertissement du navigateur indiquant que le certificat n'est pas reconnu par une autorité de certification. C'est normal, car il est auto-signé. Acceptez l'exception pour continuer.

## Conclusion

Votre serveur Apache est maintenant configuré pour utiliser SSL avec un certificat auto-signé. Cela garantit un chiffrement des communications, mais ne prouve pas l'identité du serveur.

Pour un site public, il est recommandé d'utiliser un certificat SSL émis par une autorité reconnue, comme Let's Encrypt.

Bon travail ! 🚀