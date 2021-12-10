---
title: Zabbix
description: 
published: 1
date: 2021-12-10T13:58:48.438Z
tags: 
editor: markdown
dateCreated: 2021-12-10T11:32:30.444Z
---

BANNER

# Présentation
**Zabbix** est une solution de supervision open source
 
# Installation
Pour ce tutoriel j'utilise une j'installe zabbix 5.4 sur une Debian 11, j'utilise un serveur web apache2 ainsi que mariadb pour le serveur de base de données.
> Ce tutoriel présente des failles de sécuriter, merci de l'utiliser uniquement pour effectuer des tests ou pour un réseau local.
{.is-danger}

> __Prérequis__
:arrow_right:Adresse ip statique
{.is-info}

---

Bien, pour commencer on met à jour notre système d'exploitation puis on installe quelques dépendances : apache2, mariadb, php etc..

```bash
apt update && apt upgrade
apt install apache2 php php-mysql php-mysqlnd php-ldap php-bcmath php-mbstring php-gd php-pdo php-xml libapache2-mod-php mariadb-server mariadb-client
```

Ensuite je télécharger le dépo officiel de zabbix.

```bash
cd /tmp
wget https://repo.zabbix.com/zabbix/5.4/debian/pool/main/z/zabbix-release/zabbix-release_5.4-1+debian11_all.deb
```

Une fois télécharger on installe les dépots zabbix et on met à jour la liste des dépots.

```bash
dpkg -i zabbix-release_5.4-1+debian11_all.deb
apt update
```

> Si vous avez une erreur à cette étape, éxécuter ces 3 commandes, puis recommancer l'étape précédente.
{.is-warning}

```bash
export PATH=$PATH:/usr/local/sbin
export PATH=$PATH:/usr/sbin
export PATH=$PATH:/sbin
```

Nous pouvons installer maintenant notre serveur zabbix :

```bash
apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```

On va maintenant créer une base de données ainsi que sont utilisateur : 

```bash
mysql -uroot
```

```SQL
create database zabbix character set utf8 collate utf8_bin;
create user zabbix@localhost identified by 'mon-mdp';
grant all privileges on zabbix.* to zabbix@localhost;
exit
```

Nous pouvons importez le schéma et les données initial de la base de données. Il faudra entrer le  mot de passe récemment créé.

```bash
zcat /usr/share/doc/zabbix-sql-scripts/mysql/create.sql.gz | mysql -uzabbix -p zabbix
```

Pour renseignez le mot de passe aux serveur zabbix, nous allons éditez la ligne `# DBPassword=` dans le fichier `/etc/zabbix/zabbix_server.conf`

Voici un exemple : 

```bash
DBUser=zabbix

### Option: DBPassword
#       Database password.
#       Comment this line if no password is used.
#
# Mandatory: no
# Default:
DBPassword=mon-mdp
```

Il nous reste qu'à redémarrer quelques services et les activer au démarrage de la machine.

```bash
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```

Sur votre navigateur tappez `http://server_ip/zabbix` pour finaliser l'installation de zabbix.

Séléctionnez la langue souhaitez :

MEDIA

Zabbix vérifie si tous les prérequis sont présent : 

MEDIA

Configurer votre base de données avec le mot de passe créer antérieurement :

MEDIA

Insérez le nom que vous souhaitez donné à votre serveur : 

MEDIA

Indiquer l'UTC où est situé le serveur, vous pouvez aussi modifier le thème de l'interface web :

MEDIA

Il nous reste plus qu'à valider l'installation : 

MEDIAx2

Maintenant nous pouvons nous connecter à zabbix avec le nom d'utilisateur `Admin` et le mdp `zabbix`

> Félicitation, Zabbix est maintenant installer sur votre serveur ! 
{.is-success}

# Installation Agent

Tous les agent sont disponible en téléchargement ici : https://www.zabbix.com/download_agents

## Windows

Je télécharge l'éxécutable de l'agent pour Windows, et je l'installe sur mon serveur Windows

Ici je laisse tout par défaut, j'indique juste l'ip de mon serveur Zabbix

MEDIA

Et je continue, en laisser tous les paramètres par défaut

Une fois que mon agent est installer, je peux configurer mon serveur sur l'interface web de mon serveur zabbix.

# Ajoutez un client
## Via agent
Pour ajoutez un client je vais dans l'onglet configuration puis groupes d'hôtes

Je créer un nouveau groupe d'hôte, pour ma part nommé Windows.

MEDIA

Ensuite je vais dans Configuration -> Hotes et je créer un hôte.

J'indique le nom d'hôte et je séléctionne le groupe Windows. J'ajoute une interface "Agent" et j'indique son IP, on peut également le faire via enregistrement DNS.

MEDIA

Maintenant que le client est ajouter, je peux lui appliquer une template.

Allez dans l'onglet "Modèles" puis séléctionnez la template "Windows by Zabbix agent".

MEDIA

Maintenant si je vais dans l'onglet Surveillance puis Dernières  données et qie je séléctionnez mon hôte SRV-R310 on peut voir les données récolté par l'agent.

> Félictitation, vous avez configurer votre client !
{.is-success}





