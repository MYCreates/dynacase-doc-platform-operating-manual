



















Dynacase Platform 3.1
Installation & Exploitation











Version :
1.0

Date :
Octobre 2011

Auteur :
Anakeen


Résumé
Abstract
Ce document décrit  d'installation et de mise à jour de Dynacase et en présente les principales procédure d'exploitation.
Table des matières
Dynacase Platform 3.1	1
Installation & Mise à jour	4
Introduction	4
dynacase-control	5
Téléchargement et installation	5
Première connexion	5
Interface de dynacase-control	7
dynacase-platform	14
Pré-Requis logiciels	14
Paramétrage PHP	16
Paramètres INI	16
Installation de dynacase-platform	17
Procédures d'exploitation	21
Archivage et restauration de contextes	21
Archivage	22
Restauration	22
Sauvegarde et restauration de contextes	23
Éléments d'un contexte Dynacase	23
Sauvegarde	23
Restauration	24
Tuning PostgreSQL	24
shared_buffers	25
effective_cache_size	25
work_mem	26
maintenance_work_mem	26
Conclusion	26
Liens utiles	27
Dépannage/troubleshoot	27
Connexion sécurisée (SSL)	28
Exploitation base documentaire	28
main connection db	28
unreference user in group	28
user as group	29
unreference actions	29
unreference parameters	29
unreference acl	29
unreference permission	29
connection db freedom	30
double doc id	30
double doc name	30
multiple alive revision	30
family inheritance	31
user group synchro	32
connection db webdav	32
connection USER LDAP	32
dynacase-control en mode CLI	32
Utilisation	32
Commandes	33
Lister les contextes existants	33
Manipuler les modules du contexte	33
Manipuler les paramètres des modules	37
Administration système	39
Installation & Mise à jour
Introduction
L'installation d'un contexte Dynacase nécessite au préalable l'installation de dynacase-control qui permet de créer et de gérer les contextes Dynacase.
Ensuite, dans un contexte Dynacase vous pouvez installer le module dynacase-platform et votre application spécifique.

dynacase-control
dynacase-control est l'outil qui permet d'installer et de gérer des contextes Dynacase.
Téléchargement et installation
dynacase-control est livré sous la forme d'une archive "tar.gz" à décompresser dans un répertoire servi par Apache.
L'installation s'effectuera sous le compte `root'.

Connectez-vous `root' sur la machine et déplacez-vous dans un répertoire accessible via apache (dans votre "DocumentRoot" décrit dans vos fichiers de configuration apache - par exemple) :
Pour Debian/Ubuntu :
# cd /var/www
Pour RedHat/CentOS :
# cd /var/www/html

Télécharger l'archive de dynacase-control : 
# wget http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz

Extraire l'archive et renommer le répertoire : 
# tar zxf dynacase-control-current.tar.gz
# mv dynacase-control-*-* dynacase-control

Modifier le propriétaire du répertoire de dynacase-control pour être celui de l'utilisateur faisant tourner Apache : 
Pour Debian/Ubuntu :
# chown -R www-data: dynacase-control/
Pour RedHat/Centos :
# chown -R apache: dynacase-control/

Connectez-vous à l'URL de dynacase-control : 
http://localhost/dynacase-control/
Première connexion
Define Password
Lors de la première connexion, dynacase-control vous demande de définir un login et un mot de passe pour protéger les accès à celui-ci.



EEC Registration
Ensuite, dynacase-control vous demande si vous souhaitez enregistrer ce dernier aurpès de votre compte entreprise EEC (voir Contrat EEC).



Si vous n'avez pas de compte EEC, cliquez sur le bouton "[Register later…]".
Si vous n'enregistrez pas votre dynacase-control lors de cette première connexion vous aurez la possibilité d'enregistrer celui-ci plus tard à partir de l'interface Control > Setup.
Par la suite ces informations ne vous seront plus demandés lors de la connexion à dynacase-control.
Interface de dynacase-control

Control > Setup
L'interface “Control > Setup” permet de configurer dynacase-control. 
Mettre à jour dynacase-control lorsqu'une nouvelle version est détectée
Changer le mot de passe de connexion à dynacase-control : “Dynacase Control Information > Password”
Enregistrer son dynacase-control avec son compte EEC : “Dynacase Control Information > Registration > Register”
(Dé)Activer le mode debug de dynacase-control : “Debug > Debug Mode On/Off”
Ajouter/Supprimer/Modifier des dépôt de paquets : ”Repositories”
Modifier les paramètres de dynacase-control : ”Parameters”
Voir les paramètres de PHP : “PHP Info”



Mise à jour de dynacase-control
Lors de la connexion à l'interface de dynacase-control, celui-ci vérifie si une nouvelle version est disponible.
Si une nouvelle version disponible est détectée, alors une popup propose de télécharger et d'appliquer la mise à jour :

Si l'on refuse la mise à jour, il est alors possible d'appliquer la mise à jour en allant dans la section "Setup" > "Dynacase Control Information", et de cliquer sur le bouton "[Update]".
Dépôts de paquets
La section “Repositories” permet d'ajouter, supprimer et éditer des dépôts de paquets qui pourront être utilisés pour créer un contexte Dynacase.


 
Champ 
Description 
Name 
Le nom du dépôt de paquets (caractère alpha-numérique uniquement). 
Description 
Un champ libre de description du dépôt (optionnel). 
Protocol 
Le procotol d'accès au dépôt (“http”, “ftp” ou “file”). 
Host 
Le nom d'hôte du dépôt. 
Path 
Le chemin d'accès au répertoire `webinst' du dépôt. 
Default 
Cocher pour que ce dépôt soit activé par défaut lors de la création d'un contexte Dynacase. 
Authenticated 
Cocher si le dépôt de paquets requiet une authentification (comme dans le cas d'un dépôt EEC par exemple). 
Login 
Le nom d'utilisateur (ou nom de compte EEC) pour l'authentification du dépôt. 
Password 
Le mot de passe associé pour l'authentification du dépôt. 
Confirm Password 
Confirmation du mot de passe. 

Dépôts pré-configurées
Par défaut, les dépôts de la version communautaire de Dynacase sont pré-configurées : 
Repository 
Protocol 
Host 
Path 
dynacase 
http 
eec.anakeen.com 
public/platform/ 


Paramètres de dynacase-control
Utilisation d'un proxy
Si vous utiliser dynacase-control au travers d'un proxy HTTP, vour pouvez définir celui-ci avec les paramètres :
 
Paramètre 
Description 
use-proxy 
Cocher “use proxy mode” si vous utilisez un proxy pour accéder à Internet. 
proxy-host 
Le nom DNS (ou adresse IP) du proxy HTTP (Exemple : “proxy.example.net”) 
proxy-port 
Le numéro de port du proxy HTTP (Exemple : “3128”) 
proxy-username 
Le nom d'utilisateur si le proxy requiert une authentification. 
proxy-password 
Le mot de passe associé si le proxy requiert une authentification. 

Configuration des mises-à-jour de dynacase-control
Lors du lancement de l'interface de dynacase-control, celui-ci va vérifier si une mise-à-jour est disponible.
Vous pouvez configurer avec ces paramètres l'hôte sur lequel dynacase-control effectuera les recherches et téléchargements de mises-à-jour.

Paramètre 
Valeur pré-configurée 
Description 
wiff-update-host 
http://eec.anakeen.com 
Le nom de l'hôte hébergeant les mises-à-jour avec le protocole à utiliser (“http:” ou “ftp:”) (Exemple : “http://eec.anakeen.com”). 
wiff-update-path 
/public/control/ 
Le chemin d'accès au répertoire des mises-à-jour (Exemple : ”/foo/repo/agpl-3.0/stable/tarball/”). 
wiff-update-file 
dynacase-control-current.tar.gz 
Le fichier de la version courante (Exemple : “dynacase-control-current.tar.gz”). 
wiff-update-login 

Le nom d'utilisateur si le serveur de mise-à-jour requiert une authentification. 
wiff-update-password 

Le mot de passe associé si le serveur de mise-à-jour requiert une authentification. 


Control > Create Context
L'interface “Create Context” permet de créer un nouveau contexte Dynacase.




 
Champ 
Description 
Name 
Le nom du contexte Dynacase 
Root 
Le chemin d'accès au répertoire dans lequel sera installé le contexte. Le répertoire doit être accessible en lecture/écriture par Apache. Si le répertoire spécifié n'existe pas, dynacase-control essayera de le créer. 
Description 
Un champ permettant de décrire le contexte (optionnel). 
Url 
Ce champ d'information permet de saisir l'URL par laquelle sera accessible le contexte (optionnel). 
Registration 
Si dynacase-control est enregistré avec votre compte EEC, vous pouvez enregistrer ce contexte avec votre compte EEC en cochant cette case. 
Repositories 
Cocher la liste des dépôts de paquets que vous souhaitez utiliser pour installer ce contexte. Par défaut les dépôts de paquets configurés (dans l'interface Control > Setup > Repositories) avec “Default” seront pré-sélectionnés. 

Lorsque le contexte est créé, celui-ci apparaît dans l'interface ”Context”.
Context
L'interface “Context” présente la liste des contextes créés.



Lors de la sélection d'un contexte, les informations suivantes sont présentés :
Informations
La section “Informations” présente les informations et configuration générale du contexte.
Modify Context
Cette interface permet d'éditer la description du contexte et la liste des dépôts utilisés par ce contexte.



Import Module
Cette interface permet d'importer et d'installer un module au format `.webinst' (Voir format webinst) dans le contexte.
Create Archive
Cette interface permet de Créer une archive du contexte au format `.fcz' (Voir format fcz) pour installer ce contexte sur une autre machine par exemple.



Delete context
Ce bouton permet de supprimer le contexte et ses éléments associés (base de donnée, vault, etc. 

Installed
La section “Installed” présente la liste des modules `webinst' qui sont installés dans le contexte.
Lorsqu'un module a une mise-à-jour de disponible sur les dépôts de paquets, une icône  apparaît en début de ligne d'un module et la nouvelle version disponible est présentée dans la colonne "Available Version".
Pour mettre à jour le module, il faut alors cocher la ligne du module et cliquer sur le bouton "[Upgrade Selection]".

Available
La section “Available” présente la liste des modules disponibles sur les dépôts de paquets.
Vous pouvez cocher les modules que vous souhaitez installer dans le contexte, et lancer l'installation en cliquant sur le bouton "[Install Selection]".
dynacase-platform
Une fois dynacase-control installé, il est alors possible de créer un contexte et d'installer dynacase-platform dans celui-ci.
Pré-Requis logiciels
Les informations fournies ci-dessous, le sont à titre indicatif et peuvent varier en fonction de la distribution linux cible (nom des extensions PHP et nom des paquets par exemple).
Ces informations sont fournies pour la version 3.1 de dynacase.
Les informations relatives aux composants sont exhaustives (utilisée par certain modules) et nécessaires pour une installation standard faite par défaut.
Pré-requis poste client
Navigateurs supportés
La version Dynacase 3.1 est fonctionnelle avec les navigateurs suivants :
Internet Explorer 8
Firefox 3 (>3.5)
Chrome branche stable
Pré-requis serveur Dynacase
GNU/Linux
Dynacase nécessite un système GNU/Linux (Debian, Ubuntu, RedHat, etc.)
PHP
Dynacase nécessite PHP version 5.3.
Pour les distributions qui ne fournissent pas PHP 5.3 vous pouvez télécharger et installer Zend Server avec PHP 5.3.
Extensions PHP
Les extensions notées (core) sont normalement incluse de manière statique dans PHP. 
Core (core)
SimpleXML
calendar
ctype
curl
date (core)
fileinfo
gd
gettext
iconv
imap
json
ldap
mbstring
pcntl
pcre
pgsql
posix
pspell
session (core)
sockets
standard (core)
xml
xsl
zip
Composants PEAR
XML_Parser
XML_RSS 
Net_SMTP
Mail_Mime
Crypt_CHAP (optionnel) 
PostgreSQL
Dynacase nécessite PostgreSQL version 8.4.
HTTPD Apache
Dynacase nécessite le serveur HTTPD Apache 2.2
Les modules Apache suivants sont requis :
php5_module (Voir pré-requis PHP)
env_module
expires_module
dir_module
auth_basic_module
authn_file_module
authz_host_module
setenvif_module
rewrite_module
Commandes système
Dynacase requiert les commandes système suivante :
rm
file
mkdir
tar
gzip
zip (Info-ZIP) 
unzip (Info-ZIP)
dot (Graphviz)
convert (ImageMagick)
recode
html2ps (html2ps)
ps2pdf (Ghostscript)
php (PHP en ligne de commande. Voir pré-requis php)
ldapdelete (OpenLDAP) (optional)
psql (Voir pré-requis postgresql)
pg_dump (Voir pré-requis postgresql)
msgcat (gettext)
Paramétrage PHP
Certains paramètres de PHP doivent être changés afin que Dynacase fonctionne au mieux et en fonction de votre utilisation.
Paramètres INI
date.timezone
Ce paramètre permet de spécifier le fuseau horaire utilisé par les fonctions de manipulation de dates.
date.timezone = 'Europe/Paris'
max_execution_time
Ce paramètre permet de spécifier le temps maximal (en seconde) de traitement d'une requête par PHP.
Par défaut ce paramètre est à “30”.
Exemple : 
max_execution_time = 300 ; 5 min.
max_file_uploads
Ce paramètre permet de spécifier le nombre de fichiers maximum qui seront pris en compte par PHP lors de la soumission d'un formulaire contenant des fichiers.
Par défaut ce paramètre est à “20”.
Si la valeur est trop basse, vous pourriez perdre des fichiers lors l'édition/sauvegarde de document Dynacase contenant beaucoup de fichiers.
Exemple : 
max_file_uploads = 100
upload_max_filesize
Ce paramètre permet de spécifier la taille maximale qu'un fichier uploadé peut avoir. Si un fichier d'une taille supérieure est envoyé par le navigateur, alors il ne sera pas pris en compte par PHP.
Par défaut ce paramètre est à “2M” (2 Mo).
Exemple : 
upload_max_filesize = 20M
max_post_size
Ce paramètre permet de spécifier la taille maximale d'une requête de type POST.
Par défaut ce paramètre est à “8M” (8 Mo).
Généralement, il doit être supérieur à `upload_max_filesize' et être au plus égal à `max_file_uploads' * `upload_max_filesize' :
upload_max_filesize < post_max_size ⇐ ( max_file_uploads * upload_max_filesize )
Exemple : 
post_max_size = 128M

error_reporting
Ce paramètre permet de spécifier le niveau de reporting des notices/warnings/erreurs/etc.
Il est nécessaire de ne pas afficher les messages de notices (E_NOTICE) de dépréciation (E_DEPRECATED) de PHP lors de l'utilisation de Dynacase.
Exemple : 
error_reporting = E_ALL & ~E_NOTICE & ~E_DEPRECATED
display_errors
Ce paramètre permet d'activer ou non l'affichage des erreurs PHP dans les réponses émises au client.
Par défaut ce paramètre est à “On”.
En production, il est souhaitable de désactiver le display_errors.
Exemple : 
display_errors = Off
Installation de dynacase-platform
dynacase-platform sera installé dans un contexte créé avec dynacase-control.
Avant de créer un contexte pour recevoir dynacase-platform, il vous faudra déclarer dans dynacase-control les dépôts de paquets que vous souhaitez utiliser.
Paramétrer les dépôts de paquets dans dynacase-control
La liste des dépôts que vous souhaitez utiliser est paramétrable dans la section ”Control > Setup > Repositories” de dynacase-control.
Si vous avez un compte entreprise EEC, vous pouvez aussi configurer dynacase-control pour qu'il effectue ses recherches de mises à jour sur votre dépôt dans la section ”Control > Setup > Parameters” de dynacase-control.
Créer un nouveau contexte
Une fois votre liste de dépôt renseigné, vous pouvez créer un nouveau contexte avec ”Control > Create Context”.
Installer dynacase-platform dans le contexte
Une fois votre contexte créé, celui-ci apparaît dans la liste des contexte “Context”.
Sélectionner votre contexte. Dans la section “Available” sont présentés les modules disponibles sur les dépôts configurés pour ce contexte.
Sélection des modules à installer
Dans la section “Available” des modules disponibles, sélectionner le module :
dynacase-platform
Lancer l'installation avec [Install Selection].
Résolution des dépendances
dynacase-control va vérifier les dépendances des paquets sélectionnés, et proposer la liste des paquets à installer.



Si vous êtes d'accord avec la liste proposée, vous pouvez valider cette liste et poursuivre l'installation.
Téléchargement des modules
Les modules sont téléchargés.
Licences
Les modules qui possèdent une licence vont demander votre accord.



Paramètres
Les modules peuvent demander des paramètres lors de leur installation ou upgrade.
dynacase-platform présente les paramètres suivantes lors de son installation :


 
Paramètre 
Description 
client name 
Le nom de l'installation qui apparaîtra sur la page d'accueil générique de Dynacase 
database postgresql service name 
Le nom du service postgresql pour accéder à la base dédiée pour ce contexte 
authenticate default mode 
Le mode d'authentification par défaut : “html” pour l'authentification par une page HTML et “basic” pour une authentification par le mécanisme HTTP Basic 
apache system user 
L'utilisateur sous lequel est exécuté Apache (“www-data” pour Debian/Ubuntu, “apache” pour RedHat/CentOS) 
temporary folder 
Le chemin du répertoire ou seront stockés les fichiers temporaires 
Admin password 
Le mot de passe par défaut du compte “admin” de Dyncase 
Enable compression 
Active la compression des données avec le module Apache mod_deflate 

Process d'installation
Chacun des modules sélectionné pour l'installation va suivre ce même processus d'installation :
Demande de licence
Demande des paramètres du module
Exécution des processus de pre-installation
Exécution des processus d'installation
Exécution des processus de post-installation



 
La liste des modules à installer est présenté dans la liste “Module List”.
Les modules installés avec succès sont marqués par une pastille verte.
Le panneau principal détaille l'exécution des processus de pre-installation, installation et post-installation.

Fin de l'installation
A la fin de l'installation, les modules installés sont présentés dans la section “Installed”.



Procédures d'exploitation
Archivage et restauration de contextes
dynacase-control permet de créer une archive d'un contexte qui contiendra tous les éléments du contexte en vue d'une restauration sur une autre machine par exemple.
Archivage
La fonction d'archivage d'un contexte est accessible dans dynacase-control, sur la page du contexte dans la section "Information", bouton Create Archive.
L'archive inclut les éléments suivants :
Les fichiers du contexte.
Un dump de la base de données.
Les fichiers des vaults (optionnel : il est possible de désactiver l'archivage des fichiers du vault afin de réduire la taille de l'archive).
Les archives sont stockés dans le sous-répertoire `archived-contexts' de dynacase-control avec une extension `.fcz'.
Restauration
Les archives `.fcz' présentes dans le sous-répertoire `archived-contexts' de dynacase-control sont présentés dans la section "Archives" de l'interface de dynacase-control, sous la liste "Context".



Pour restaurer une archive il faut cliquer sur le bouton "[Create Context]" et remplir les informations demandés :



Root : Le chemin d'accès du répertoire dans lequel les fichiers du contexte seront restaurés.
Core Database Service : Le nom du service PostgreSQL dans lequel sera restauré le dump de la base de données.
Vault Root : Le chemin d'accès du répertoire dans lequel les fichiers du vault seront restaurés.

Ces répertoires doivent être créés manuellement et être accessibles en écriture à l'utilisateur Apache.
Sauvegarde et restauration de contextes
La sauvegarde se différencie de l'archivage par le fait que la sauvegarde est une opération effectuée par le service d'exploitation système, et qu'il peut s'intégrer plus facilement dans les procédures mises en place par le service informatique.
Ce chapitre va donc présenter les éléments à sauvegarder en se focalisant sur les aspects systèmes.
Éléments d'un contexte Dynacase
Un contexte Dynacase est composé des éléments suivants :
Le répertoire du contexte : il contient le code PHP des applications et des familles de documents.
La base de données : elle contient les documents et le paramétrage Dynacase.
Le ou les vaults : ils contiennent les fichiers insérés dans les documents Dynacase.
Sauvegarde
wstop
Afin d'assurer l'intégrité des données, l'accès au contexte sera bloqué afin que les utillisateurs ne puissent pas modifier les données durant le déroulement de la sauvegarde.
Pour cela il faudra lancer la commande `wstop' dans le contexte Dynacase qu'on sauvegarde :
# /var/www/dynacase-control/wiff wstop <nomDuContexte>

Sauvegarde du répertoire du contexte
Le répertoire du contexte contient des ficheirs et des répertoires qui peuvent être sauvegardés de manière standard avec des outils comme `tar' : 
# tar zcf /backup/dynacase.context.tar.gz /var/www/dynacase

Afin de réduire la durée de sauvegarde vous pouvez exclure les éléments suivant de la sauvegarde de ce répertoire et qui seront régénérés dynamiquement après la restauration :
sessions/ : sous-répertoire contenant les sessions PHP.
.img-resize/ : sous-répertoire de cache d'images.

Vous pouvez aussi effectuer une sauvegarde incrémentale des fichiers du répertoire du contexte.

Sauvegarde de la base de données
La sauvegarde de la base de données s'effectue par un dump complet à l'aide de la commande `pg_dump' :
# PGSERVICE=dynacase pg_dump | gzip > /backup/dynacase.pg_dump.gz

Sauvegarde du vault
Le vault, comme le répertoire du contexte, peut être sauvegardé avec des outils standards comme `tar' :
# tar zcf /backup/dynacase.vault.tar.gz /var/vault

Une sauvegarde incrémentale est aussi possible pour ce répertoire.

wstart
Une fois les éléments sauvegardés, l'accès au contexte peut-être ré-ouvert :
# /var/www/dynacase-control/wiff wstart <nomDuContexte>

Restauration
La restauration des éléments s'effectue de manière inverse à leur sauvegarde.
Restauration des vaults
Restauration des fichiers précédemment archivés :
# tar -C / -zxf /backup/dynacase.vault.tar.gz

Restauration de la base de données
Restauration du dump de la base de données :
# gzip -dc /backup/dynacase.pg_dump.gz | PGSERVICE=dynacase psql

Restauration du répertoire du contexte
Restauration des fichiers du contexte :
# tar -C / -zxf /backup/dynacase.context.tar.gz

Ré-enregistrement des crontab
# /var/www/dynacase-control/wiff context <nomDuContexte>
wstart
Comme la sauvegarde a été effectués en mode maintenance `wstop', il faut à la fin ré-ouvrir l'accès avec `wstart' :
# /var/www/dynacase-control/wiff wstart <nomDuContexte>

Tuning PostgreSQL
Dans le cadre de son utilisation avec Dynacase, une couche d’abstraction logicielle Dynacase sur PostgreSQL permet d’optimiser son utilisation. Les éventuels tuning de la base PostgreSQL dépendent entièrement du modèle documentaire mis en œuvre par Dynacase. Ils sont réalisés ou préconisés si nécessaire lors de la mise en œuvre de la solution par un expert Anakeen. 

La plupart des distributions livrent PostgreSQL avec une configuration basique, passe-partout, qui n'est pas forcement adaptée au volume et au nombre de transaction que peut générer Dynacase.
Voici quelques éléments de configuration pour une machine type avec 2 Go à 3 Go de RAM.
shared_buffers
Le paramètre shared_buffers permet de spécifier la taille de la mémoire partagée allouée par Postgresql.
Il est à noter que ce paramètre PostgreSQL dépend d'un paramètre du kernel qui indique la valeur maximale de mémoire partagée disponible pour les process. Il faudra donc au préalable augmenter la valeur de ce paramètre au niveau du noyau, pour ensuite augmenter la valeur dans PostgreSQL.
Sous Linux, la taille de la mémoire partagée maximale autorisée est définie par le paramètre sysctl kernel.shmmax :
# sysctl kernel.shmmax kernel.shmmax = 33554432

On voit dans ce cas que la valeur est de 32 Mo
Sur une machine dédiée à PostgreSQL avec 2 à 3 Go de RAM on alloue entre 1 et 2 Go de mémoire partagée utilisable par les process :
# sysctl -w kernel.shmmax=2147483648 kernel.shmmax = 2147483648

Pour rendre ce paramètre persistant au redémarrage, on enregistre celui-ci dans le fichier /etc/sysctl.conf.

Une fois le shmmax configuré sur le kernel, on définit le paramètres PostgreSQL shared_buffers.

# vi /var/lib/pgsql/data/postgresql.conf
[...]
shared_buffers = 1024MB
[...]

La prise en compte de ce paramètre est effective après re-démarrage de PostgreSQL :
# /etc/rc.d/init.d/postgresql restart

Si Postgresql ne redémarre pas, vérifier que la valeur de shared_buffers est bien inférieure à la valeur de kernel.shmmax.
effective_cache_size
Ce paramètre indique à PostgreSQL la mémoire restante une fois que PostgreSQL et tous les autres process du serveur tournent en fonctionnement normal. Cet espace « libre » est utilisé par le noyau pour gérer ses buffers et son cache. Cet espace est visualisable sous Linux avec la commande free sous la forme des valeurs de buffers et cached :
# free
             total       used       free    shared    buffers     cached
Mem:       3095688    1932116    1163572         0      62524    1433776
-/+ buffers/cache:     435816    2659872
Swap:      1044208      89816     954392

Dans le cas ci-dessus, on peut donc indiquer à PostgreSQL une valeur de effective_cache_size de 1400 Mo :
# vi /var/lib/pgsql/data/postgresql.conf
[...]
effective_cache_size = 1400MB
[...]

work_mem
Le paramètre work_mem spécifie la quantité de mémoire que peut utiliser un process PostgreSQL pour effectuer des tris. Une valeur plus grande lui permettra de pouvoir manipuler plus de données en mémoire vive (plutôt que d'avoir recours à des fichiers temporaires qui sont plus lents).
Ce paramètre doit être calibré en fonction du nombre maximum de connexions concurrentes déclaré par le paramètre max_connections. La mémoire allouée pour ces opérations étant alors égale à max_connections * work_mem.
# vi /var/lib/pgsql/data/postgresql.conf
[...]
max_connections = 64
[...]
work_mem = 16Mb
[...]

Dans l'exemple ci-dessus, l'utilisation mémoire pourra monter à 64 * 16 Mo = 1 Go.
maintenance_work_mem
Le paramètre maintenance_work_mem spécifie la quantité de mémoire que peuvent utiliser les commandes/process PostgreSQL de VACUUM, création d'index et altération de table. Une valeur plus grande lui permettra de pouvoir manipuler plus de données en mémoire vive, et accélèrera le temps de traitement sur les VACUUM (freedom_clean) par exemple.
# vi /var/lib/pgsql/data/postgresql.conf
[...]
maintenance_work_mem = 128Mb
[...]

Conclusion
Ces paramètres permettent donc de donner une plus grande liberté de mouvement à PostgreSQL comparés à ceux livrés par défaut.
Les valeurs données ci-dessus sont des exemples qu'il faudra bien sûr adapter/moduler en fonction de la machine et de son utilisation dans le temps.
Liens utiles
http://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server
Dépannage/troubleshoot
Voir les requêtes en cours d'exécution : pg_stat_activity
Pour voir les requêtes en cours d'exécution, Postgresql propose la table `pg_stat_activity' :
postgres=# SELECT * FROM pg_stat_activity;
datid | datname | procpid | usesysid | usename | application_name | client_addr | client_port | backend_start | xact_start | query_start | waiting | current_query --------+----------+---------+----------+----------+------------------+-------------+-------------+-------------------------------+-------------------------------+-------------------------------+---------+---------------------------------
 11874 | postgres | 40516 |    10 | postgres | psql90 |     | -1    | 2011-01-17 11:49:52.022024+01 | 2011-01-17 11:53:43.766623+01 | 2011-01-17 11:53:43.766623+01 | f | SELECT * FROM pg_stat_activity;
207531 | 3.0.16   | 40570 | 16391 | freedom  |        | ::1 | 57914 | 2011-01-17 11:50:28.902947+01 |                               | 2011-01-17 11:51:30.213533+01 | f | VACUUM FULL ANANLYSE;
(2 rows)

Cette table présente le détail des requêtes en cours d'exécution (current_query), le pid du process qui execute cette requêtes (procpid), l'heure de prise de reception de la requête (backend_start), l'heure de début de traitement de la requête (query_start) et le caractère « waiting » (waiting) d'une requête si elle est bloqué ou en attente de libération d'une ressource par une autre requête.
La visualisation du champ `current_query' nécessite le paramètre `track_activities = on' (par défaut à `on'). Si ce paramètre n'était pas actif, vous pouvez le dfinir dans postgresql.conf et recharger la conf à chaud :
# vi /path/to/postgresql.conf
[...]
track_activities = on
[...]

# psql
postgres=# SELECT pg_reload_conf();
postgres=# SHOW track_activities;

Logger les requêtes longues : log_min_duration_statement
Lorsque l'observation de longues requêtes par `pg_stat_activity' n'est pas possible (parce que le problème n'est pas reproductible ou observable à une heure précise de la journée par ex.) on peut indiquer à Postgresql de logger les requêtes qui mettent plus N secondes à s'exécuter.
La durée est paramétrable via le paramètre `log_min_duration_statement' (par défaut `-1' : désactive le log). Vous pouvez soit le définir en global dans postgresql.conf, soit par base de données (via un ALTER DATABASE) : 
Configuration globale commune à toutes les bases (ex. 5 minutes) :
# vi /path/to/postgresql.conf
[...]
log_min_duration = 5min
[...]

# psql
postgres=# SELECT pg_reload_conf();

Paramétrage sur une base spécifique (ex. 5 minutes) :
# psql
postgres=# ALTER DATABASE dynacase SET log_min_duration_statement = '5min';

Les logs apparaissent alors dans `postgresql.log' sous la forme :
2011-01-11 17:15:01 CET LOG: duration: 2.291 ms statement: SELECT * from paramv where type='G' or (type='A' and appid=1); </pre>
Connexion sécurisée (SSL)
Configuration client :
SSL Support (http://www.postgresql.org/docs/8.3/interactive/libpq-ssl.html)
Configuration serveur :
Secure TCP/IP Connections with SSL (http://www.postgresql.org/docs/8.3/interactive/ssl-tcp.html)

Utilisation de la directive `sslmode=require' dans `pg_service.conf' pour forcer la connexion du client par SSL : 
[dynacase]
host=pg.example.net
port=5432
user=dynacase
password=secret
dbname=dynacase
sslmode=require

Exploitation base documentaire
Une liste de vérification de problèmes pouvant se produire sur une installation Dynacase est disponible dans le sous-répertoire `admin' du contexte et accessible par l'URL :
http://<nomDuServeur>/admin/checklist.php

L'interface présente alors une liste de vérification avec un statut : vert pour "OK", rouge pour "KO".
main connection db
Signification : 
Remède : 
Vérifier que Postgresql est bien lancé
unreference user in group
Signification : 
Remède : 
Supprimer les références : 
DELETE FROM groups where iduser not in (select id from users);
user as group
Exemple : 
user as group1 users detected as group 25
Signification : 
Peut arriver par un import sql qui écraserait les utilisateurs
Remède : 
Soit mettre les utilisateurs comme groupe (mettre l'attribut isgroup à Y dans la table users) ou supprimer la référence dans la table groupe : 
dynacase=# BEGIN;
dynacase=# DELETE FROM groups WHERE idgroup not in (select id from users where isgroup='Y');
DELETE 110
dynacase=# COMMIT;
COMMIT
Et exécuter cette commande :
# /var/www/dynacase-control/wiff context <nomDuContexte> exec ./wsh --api=freedom_groups
unreference actions
Signification : 
Peut arriver lors de suppression d'applications
Remède : 
Appliquer le fichier sql /var/www/dynacase/WHAT/what_clean.sql sur la base principale
unreference parameters
Signification : 
Peut arriver lors de suppression d'applications
Remède : 
Appliquer le fichier sql /var/www/dynacase/WHAT/what_clean.sql sur la base principale
unreference acl
Signification : 
Remède : 
Appliquer le fichier sql /var/www/dynacase/WHAT/what_clean.sql sur la base principale
unreference permission
Signification : 
Remède : 
Appliquer le fichier sql /var/www/dynacase/WHAT/what_clean.sql sur la base principale
connection db freedom
Signification : 
Idem. "main connection db"
Remède : 
double doc id
Exemple : 
1 double id detected
Array
(
    [1178] => 2
)
Signification : 
Plusieurs documents ont le même identifiant numérique. Peut arriver lors d'importation avec des identificateurs fixe ou lors d'importation avec des familles inexistantes.
Remède : 
Supprimer l'un des doublons (ou les deux)
dynacase=# SELECT id,count(*) FROM doc GROUP BY id HAVING count(*) > 1;
  id  | count
------+-------
 1178 |     2

Détail des enregistrements posant problème : 
dynacase=# SELECT id, title, fromid, locked FROM doc WHERE id = 1178;
  id  |      title      | fromid | locked
------+-----------------+--------+--------
 1178 | Fiche de suivi  |      0 |      0
 1178 | Dossier EI auto |   1169 |      0

Suppression de l'enregistrement incorrecte :
dynacase=# DELETE FROM doc WHERE id = 1178 AND fromid = 0;
DELETE 1
double doc name
Signification : 
Plusieurs documents ont le même identifiant logique (attribut name)
Remède : 
Supprimer l'un des doublons (ou les deux) 
multiple alive revision
Exemple : 2 multiple alive
Array (
  [73780] => jean-raymond.durand@example.net
  [85480] => Restauration groupe test
)
Signification : 
Plusieurs révisions “vivante” sur un même document. Cela peut arriver sur d'ancienne version (< 2.14) lorsque deux tentatives de révisions simultanées sont créés ou si par programmation on force des restauration.
Remède : 
dynacase=# SELECT id, title FROM docread WHERE id IN (SELECT m AS id FROM (SELECT min(id) AS m, initid, count(initid) AS c FROM docread WHERE locked != -1 AND doctype != 'T' GROUP BY docread.initid) AS z WHERE z.c > 1);
 id   |                        title 
-------+-----------------------------------------------------
 73780 | jean-raymond.durand@example.net
 85480 | Restauration groupe test
(2 lignes)

# /var/www/dynacase-control/wiff context <nomDuContexte> exec ./wsh.php --api=fixMultipleAliveRevision
Fixing mutiple alive revision for (initid='35198', id='85480')
Fixing mutiple alive revision for (initid='56405', id='73780')
family inheritance
Exemple : 
Family [44232]: fromid = 0, pg inherit=
Family [44237]: fromid = 0, pg inherit=
Family [44241]: fromid = 0, pg inherit=
...
Signification : 
L'héritage déclaré par la famille ne correspont pas à celle défini dans postgresql. Peut arriver si on change l'héritage d'une famille ou si une famille est créée avant la famille père
Remède : 
Identifier les enregistrement posant problème : 
dynacase=# SELECT id, title, fromid, locked, doctype FROM doc WHERE fromid = 0 AND doctype IS NULL;
  id   | title | fromid | locked | doctype
-------+-------+--------+--------+---------
 44232 |       |      0 |      0 |
 44237 |       |      0 |      0 |
 44241 |       |      0 |      0 |
...

Supprimer les enregistrements :
dynacase=# DELETE FROM doc WHERE fromid = 0 AND doctype IS NULL;
DELETE 14
Ou supprimer le table correspondante et lancer :
# /var/www/dynacase-control/wiff context <nomDuContexte> exec ./wsh --api=fdl_adoc --docid=<N°famille>
user group synchro
Exemple 
inheritance tables group are differents between user=anakeen dbname=anakeen 
and user=anakeen dbname=freedom connection db webdavdbnam
Signification : 
Les groupes définis dans la base principale ne sont pas les mêmes que celle de la base FREEDOM
Remède : 
Lancer : 
# /var/www/dynacase-control/wiff context <nomDuContexte> exec ./wsh --api=freedom_groups
connection db webdav
Signification : 
Si le module WORKSPACE est installé
Remède : 
connection USER LDAP
Signification : 
Si le module NETWORKUSER est installé
Remède : 
dynacase-control en mode CLI
Le mode CLI de dynacase-control permet d'administrer les contextes Dynacase en ligne de commande.
Utilisation
La commande `wiff' permet de manipuler et administrer les contextes Dynacase-platform.
A terme, les opérations actuellement accessibles à travers l'UI Web, seront accessibles en ligne de commande à l'aide de cette commande `wiff'.
La commande `wiff' doit être lancé sous `root'. Pour cela, vous pouvez soit vous logger sous le compte `root' pour exécuter la commande, soit utiliser `sudo pour exécuter celle-ci.
Si vous n'êtes pas `root' sur le serveur, et que les opérations d'administrations s'effectuent avec `sudo' :
$ sudo /var/www/dynacase-control/wiff help
Password: ******
[message d'aide]
Si vous avez accès au compte `root' sur le serveur :
# /var/www/dynacase-control/wiff help
[message d'aide]
Par la suite, nous utiliserons la notation de ce dernier cas (`# /var/www/dynacase-control/wiff').
Commandes
Lister les contextes existants
Syntaxe
list context [--pretty]
Description
Cette opération permet d'afficher les contextes installés. 
Exemple
# /var/www/dynacase-control/wiff list context
developpement
test
production
# /var/www/dynacase-control/wiff list context --pretty
Name               Description                                                     
-----------------------------------------------------------------------------------
developpement      Développement
test               Test
production         Production
Manipuler les modules du contexte
Lister les modules installés
Syntaxe
context <context-name> module list installed [--pretty]

Description
Cette opération affiche les modules installés dans le contexte sélectionné.
Exemple
# /var/www/dynacase-control/wiff context test module list installed
dynacase-fckeditor (2.6.3-7)
dynacase-webdesk (1.2.2-1)
dynacase-extjs (3.1.1-8)
dynacase-toolbox (3.0.11-1)
dynacase-extui (0.6.4-1)
dynacase-workspace (0.6.1-1)
dynacase-ecm (0.3.0-1)
Lister tous les modules disponibles à l'installation
Syntaxe
context <context-name> module list available [--pretty]
Description
Cette opération affiche la liste des modules disponibles sur les dépôt de paquets accessibles par HTTP ou FTP.
Exemple
# /var/www/dynacase-control/wiff context test module list available
dynacase-extjs (3.1.1-8)
dynacase-toolbox (3.0.11-1)
dynacase-fckeditor (2.6.3-7)
dynacase-freeevent (2.6.0-0)
dynacase-webdesk (1.2.2-1)
[...]
dynacase-apisamples (0.1.3-1)
dynacase-familysamples (0.1.0-2)
dynacase-cas (0.0.2-1)
dynacase-theme (0.0.1-4)
dynacase-url (0.0.0-3)
)
Lister les modules installés ayant une mise-à-jour de disponible
Syntaxe
context <context-name> module list upgrade [--pretty]
Description
Cette opération permet d'afficher la liste des modules installés dont une mise-à-jour est disponible sur les dépôts de paquets.
Exemple
# /var/www/dynacase-control/wiff context test module list upgrade
dynacase-platform (2.14.1-12)
Installer un module
Syntaxe
context <context-name> module install [options] <localPackagePath>
context <context-name> module install [options] <moduleName>
Description
Cette opération permet d'installer un module contenu dans un paquet local (archive .webinst stocké en local sur le serveur) ou à partir d'un paquet disponible sur un dépôt de paquets distant accessible par HTTP ou FTP.
Les options disponibles sont :
`–nopre' permet de ne pas exécuter les processus de `pre-install' déclarés par le paquet.
`–nopost' permet de ne pas exécuter les processus de `post-install' déclarés par le paquet.
`–nothing' permet de ne pas exécuter les processus de `pre-install' et de `post-install' déclarés par le paquet.
`–force' permet de forcer l'installation du paquet même si celui-ci est déjà installé.
Exemple
Installation d'un paquet distant : 
# /var/www/dynacase-control/wiff context test module install dynacase-platform
Will install (or upgrade) the following packages:
- dynacase-vault-3.10.0-0 
- dynacase-fckeditor-2.6.3-5 
- dynacase-common-0.6.0-0 
- dynacase-platform-2.14.3-18 
Proceed to installation ? [Y/n] Y

Processing required module 'dynacase-platform' (2.14.1-12) for install.
Downloading module 'dynacase-platform-2.14.1-12'... [OK]

Parameters for module 'dynacase-platform'
------------------------------------

client_name ? [] Test

core_db ? [test] 

authtype ? [html] 

apacheuser ? [www-data] _www

Doing 'pre-install' of module 'dynacase-platform'.
[...]
Doing 'unpack' of module 'dynacase-platform'.
Unpacking module 'dynacase-platform'... [OK]
Doing 'post-install' of module 'dynacase-platform'.
Running 'Initialize system database'... [OK]
Running 'Record core application in database'... [OK]
Running 'Record users application in database'... [OK]
Running 'Record access application in database'... [OK]
Running 'Record authent application in database'... [OK]
Running 'Record appmng application in database'... [OK]
Running 'Generate traduction catalog'... [OK]
Running 'Register client name'... [OK]

Processing required module 'dynacase-vault' (3.10.0-0) for install.
Downloading module 'dynacase-vault-3.10.0-0'... [OK]
Doing 'pre-install' of module 'dynacase-vault'.
Doing 'unpack' of module 'dynacase-vault'.
Unpacking module 'dynacase-vault'... [OK]
Doing 'post-install' of module 'dynacase-vault'.
Running 'Process programs/record_application VAULT'... [OK]
Running 'Process wsh.php --api=vault_init'... [OK]
Running 'Process programs/update_catalog'... [OK]

Processing required module 'dynacase-fckeditor' (2.6.3-5) for install.
Downloading module 'dynacase-fckeditor-2.6.3-5'... [OK]
Doing 'pre-install' of module 'dynacase-fckeditor'.
Doing 'unpack' of module 'dynacase-fckeditor'.
Unpacking module 'dynacase-fckeditor'... [OK]
Doing 'post-install' of module 'dynacase-fckeditor'.

Processing required module 'dynacase-common' (0.6.0-0) for install.
Downloading module 'dynacase-common-0.6.0-0'... [OK]
Doing 'pre-install' of module 'dynacase-common'.
Doing 'unpack' of module 'dynacase-common'.
Unpacking module 'dynacase-common'... [OK]
Doing 'post-install' of module 'dynacase-common'.
Running 'Process programs/record_application FDC'... [OK]
Running 'Process programs/update_catalog'... [OK]

Processing required module 'freedom' (2.14.3-18) for install.
Downloading module 'dynacase-2.14.3-18'... [OK]
Doing 'pre-install' of module 'freedom'.
Running 'Check php function gd_info'... [OK]
Running 'Check php function cal_info'... [OK]
Running 'Check php class XSLTProcessor'... [OK]
Doing 'unpack' of module 'freedom'.
Unpacking module 'freedom'... [OK]
Doing 'post-install' of module 'freedom'.
Running 'Process programs/app_post FDL I'... [OK]
Running 'Process programs/record_application FDL I'... [OK]
Running 'Process programs/app_post FDL U'... [OK]
Running 'Process programs/app_post USERCARD I'... [OK]
Running 'Process programs/record_application USERCARD I'... [OK]
Running 'Process programs/app_post USERCARD U'... [OK]
Running 'Process programs/record_application GENERIC I'... [OK]
Running 'Process programs/record_application ONEFAM I'... [OK]
Running 'Process programs/record_application FUSERS I'... [OK]
Running 'Process programs/app_post FREEDOM I'... [OK]
Running 'Process programs/record_application FREEDOM I'... [OK]
Running 'Process programs/app_post FREEDOM U'... [OK]
Running 'Process programs/record_application FGSEARCH I'... [OK]
Running 'Process wsh.php --api=crontab --cmd=register --file=FREEDOM/freedom.cron'... [OK]
Running 'Process programs/update_catalog'... [OK]

Done.
Installation d'un paquet local :
# /var/www/dynacase-control/wiff context test module install /tmp/foo-1.2.3-4.webinst
Processing required module 'foo' (1.2.3-4) for install.
Module 'foo-1.2.3-4' is already downloaded in '/private/tmp/foo-1.2.3-4.webinstkeZbf9'.
Doing 'pre-install' of module 'foo'.
Running 'Check php class XML_Parser'... [OK]
Running 'Check php function imap_open'... [OK]
Doing 'unpack' of module 'foo'.
Unpacking module 'foo'... [OK]
Doing 'post-install' of module 'foo'.
Running 'Process programs/app_post FOO I'... [OK]
Running 'Process programs/record_application FOO'... [OK]
Running 'Process programs/app_post FOO U'... [OK]
Running 'Process programs/update_catalog'... [OK]

Done.
Upgrader un module
Syntaxe
context <context-name> module upgrade [options] <localPackagePath>
context <context-name> module upgrade [options] <moduleName>
Description
Cette opération permet d'upgrader un module à partir d'un paquet local (archive .webinst stocké en local sur le serveur) ou à partir d'un paquet disponible sur un dépôt de paquets distant accessible par HTTP ou FTP.
Les options disponibles sont :
`–nopre' permet de ne pas exécuter les processus de `pre-upgrade' déclarés par le paquet.
`–nopost' permet de ne pas exécuter les processus de `post-upgrade' déclarés par le paquet.
`–nothing' permet de ne pas exécuter les processus de `pre-upgrade' et de `post-upgrade' déclarés par le paquet.
`–force' permet de forcer l'upgrade du paquet même si une version supérieure ou égale est déjà installée.
Exemple
# /var/www/dynacase-control/wiff context test module upgrade /tmp/foo-2.0.0-1.webinst
Processing required module 'foo' (2.0.0-1) for upgrade.
Module 'foo-2.0.0-1' is already downloaded in '/private/tmp/foo-2.0.0-1.webinstwEvSU3'.
Doing 'pre-upgrade' of module 'foo'.
Running 'Check php class XML_Parser'... [OK]
Running 'Check php class XML_RSS'... [OK]
Running 'Check php function imap_open'... [OK]
Doing 'unpack' of module 'foo'.
Unpacking module 'foo'... [OK]
Doing 'post-upgrade' of module 'foo'.
Running 'Process programs/pre_migration FOO'... [OK]
Running 'Process programs/app_post FOO U'... [OK]
Running 'Process programs/record_application FOO'... [OK]
Running 'Process programs/post_migration FOO'... [OK]
Running 'Process programs/update_catalog'... [OK]

Done.
Manipuler les paramètres des modules
Afficher l'ensemble des paramètres d'un ou des modules
Syntaxe
context <context-name> param show [<moduleName>]
Description
Cette opération permet d'afficher la liste des paramètres des modules installés.
Exemple
# /var/www/dynacase-control/wiff context test param show 
dynacase-platform:client_name = Test
dynacase-platform:core_db = test
dynacase-platform:authtype = html
dynacase-platform:apacheuser = _www
foo:bar = baz
foo:bir = biz

# /var/www/dynacase-control/wiff context test param show foo
foo:bar = baz
foo:bir = biz
Afficher la valeur d'un paramètre d'un module
Syntaxe
context <context-name> param get <module-name>:<param-name>
Description
Cette opération permet d'afficher la valeur d'un paramètre d'un module donné.
Exemple
# /var/www/dynacase-control/wiff context test param get foo:bar
foo:bar = baz
Positionner la valeur d'un paramètre d'un module
Syntaxe
context <context-name> param set <module-name>:<param-name> <param-value>
Description
Cette opération permet de positionner la valeur d'un paramètre d'un module donné.
Exemple
# /var/www/dynacase-control/wiff context <context-name> param set foo:bar buzz
foo:bar = buzz
Exécuter wstop sur un contexte
Syntaxe
wstop <context-name>
Description
Cette opération permet d'exécuter le script historique `wstop' sur un contexte.
Exemple
# /var/www/dynacase-control/wiff wstop test
check update needed (/var/www/test/wcheck)
Exécuter wstart sur un contexte
Syntaxe
wstart <context-name>
Description
Cette opération permet d'exécuter le script historique `wstart' sur un contexte.
Exemple
# /var/www/dynacase-control/wiff wstart test
Exécuter whattext sur un contexte
Syntaxe
whattext <context-name>
Description
Cette opération permet d'exécuter le script historique `whattext' sur un contexte.
Exemple
# /var/www/dynacase-control/wiff whattext test
Administration système
Ouvrir un shell sous l'uid Apache
Syntaxe
context <context-name> shell
context <context-name> exec  <command> [<command-options>]]
Description
Cette opération permet d'ouvrir un shell, ou d'exécuter une commande, sous l'uid de l'utilisateur exécutant le serveur Apache afin d'effectuer manuellement des opérations d'administration spécifiques.
Par défaut, si aucune commande n'est spécifié, le shell par défaut définit pour l'utilisateur du serveur Apache est lancé. Si l'utilisateur n'a pas de shell associé, il faudra alors spécifier le chemin du shell qu'on souhaite exécuter avec la variante `exec'.
Lors de l'ouverture du shell, ou de l'exécution de la commande, les variables d'environnement suivantes sont pré-positionnés :
`HOME' : le répertoire racine du contexte.
`wpub' : le répertoire racine du contexte (pour compatibilité scripts post/migr de Dynacase).
`pgservice_core' : le service Postgresql de la base `core' (pour compatibilité scripts post/migr de Dynacase).
`pgservice_freedom' : le service Postgresql de la base `freedom' (pour compatibilité scripts post/migr de Dynacase).
`freedom_context' : vaut toujours ”default” (pour compatibilité scripts post/migr de Dynacase).
Exemple
# /var/www/dynacase-control/wiff context test shell /bin/bash
wiff(test)~$ id
uid=70(_www) gid=70(_www) egid=20(staff) groups=70(_www)
wiff(test)~$ pwd
/var/www/test
wiff(test)~$ ./FOO/FOO_post U
[...]

# /var/www/dynacase-control/wiff context test exec /usr/bin/tar -zcf /tmp/archive.tar.gz .
