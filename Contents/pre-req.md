# Pré-requis


Ce chapitre identifie l'ensemble des pré-requis pour l'installation et l'utilisation de Dynacase Platform.

Seuls les pré-requis pour les modules standards sont listés.


## Nouveautés 3.2

### php-intl 
Installer le module php Intl (php5-intl habituellement) et penser à relancer le service apache.
Ce module est utilisé pour l'internationalisation.

### Implémentation libc de Iconv
La fonction `iconv` doit être fournie par PHP compilé avec GLIBC.
Pour vérifier cela :  

    [bash]
    $ iconv --version
    iconv (Ubuntu EGLIBC 2.11.1-0ubuntu7.8) 2.11.1  
    ...
    $ php -r 'var_dump(ICONV_IMPL); var_dump(iconv("UTF-8", "ASCII//TRANSLIT", "\xc3\xa9t\xc3\xa8"));'  
    string(5) "glibc"
    string(3) "ete"   

## Poste client

Les navigateurs supportés sont :

* Internet Explorer 8 & 9
* Firefox > 3.6
* Chrome branche stable

## Serveur

### GNU/Linux
Dynacase fonctionne sur un système GNU/Linux (Debian, Ubuntu, RedHat, etc.).

#### Commandes système

Dynacase requiert les commandes système suivantes :

* `rm`
* `file`
* `mkdir`
* `tar`
* `gzip`
* `zip` (En cas de problème avec la commande zip, utiliser la version fournie par [Info-ZIP](http://www.info-zip.org/))
* `unzip` (En cas de problème avec la commande zip, utiliser la version fournie par [Info-ZIP](http://www.info-zip.org/))
* `dot` (fourni par [Graphviz](http://graphviz.org/))
* `convert` (fourni par [ImageMagick](http://www.imagemagick.org/))
* `recode`
* `html2ps`
* `ps2pdf` (fourni par [Ghostscript](http://www.ghostscript.com/))
* `php` (PHP en ligne de commande. Voir [les pré-requis php](#pre-req-php))
* `psql` (Voir [les pré-requis postgresql](#pre-req-postgresql))
* `pg_dump` (Voir [les pré-requis postgresql](#pre-req-postgresql))
* `msgcat` (fourni par [gettext](http://www.gnu.org/software/gettext/gettext.html))
* `ldapdelete` (fourni par [OpenLDAP](http://www.openldap.org/)) (*optionnel*)

### PHP {#pre-req-php}

Dynacase Platform nécessite PHP en version **5.3**.

#### Zend Server
Pour les distributions qui ne fournissent pas PHP 5.3 vous pouvez télécharger et installer Zend Server avec PHP 5.3.

#### Extensions PHP
Les extensions notées (core) sont normalement incluses de manière statique dans PHP. 

* Core (core)
* SimpleXML
* calendar
* ctype
* curl
* date (core)
* fileinfo
* gd
* gettext
* iconv (GLIBC)
* imap
* intl
* json
* ldap
* mbstring
* pcntl
* pcre
* pgsql
* posix
* pspell
* session (core)
* sockets
* standard (core)
* xml
* xsl
* zip

#### Composants PEAR

* XML_Parser
* XML_RSS 
* Net_SMTP
* Mail_Mime
* Crypt_CHAP (optionnel) 

#### Paramétrage PHP

Certains paramètres de PHP doivent être modifiés afin que Dynacase Platform fonctionne au mieux et en fonction de votre utilisation. Ces valeurs préconisées doivent être revues en fonction de votre configuration réelle et de vos applications.

##### Paramètres INI

date.timezone
:   Ce paramètre permet de spécifier le fuseau horaire utilisé par les fonctions de manipulation de dates.

    [ini]
    date.timezone = 'Europe/Paris'

max_execution_time
:   Ce paramètre permet de spécifier le temps maximal (en seconde) de traitement d'une requête par PHP.  
Par défaut ce paramètre est à “30”.

    [ini]
    max_execution_time = 300 ; 5 min.

max_file_uploads
:   Ce paramètre permet de spécifier le nombre de fichiers maximum qui seront pris en compte par PHP lors de la soumission d'un formulaire contenant des fichiers.
Si la valeur est trop basse, vous ne pourrez pas sauvegarder des fichiers lors la sauvegarde de document lorsqu'un document contient beaucoup de fichiers.  
Par défaut ce paramètre est à “20”.

    [ini]
    max_file_uploads = 100

upload_max_filesize
:   Ce paramètre permet de spécifier la taille maximale qu'un fichier téléversé peut avoir. Si un fichier d'une taille supérieure est envoyé par le navigateur, alors il ne sera pas pris en compte par PHP.  
Par défaut ce paramètre est à “2M” (2 Mo).

    [ini]
    upload_max_filesize = 20M

max_post_size
:   Ce paramètre permet de spécifier la taille maximale d'une requête de type POST.
Généralement, il doit être supérieur à 'upload_max_filesize' et être au plus égal à 'max_file_uploads' * 'upload_max_filesize' : upload_max_filesize < post_max_size ⇐ ( max_file_uploads * upload_max_filesize )  
Par défaut ce paramètre est à “8M” (8 Mo).

    [ini]
    post_max_size = 128M

error_reporting
:   Ce paramètre permet de spécifier le niveau de reporting des notices/warnings/erreurs/etc. Il est nécessaire de ne pas afficher les messages de notices (*E_NOTICE*) de dépréciation (*E_DEPRECATED*) de PHP lors de l'utilisation de Dynacase Platform (hors phase de développement et de mise au point).

    [ini]
    error_reporting = E_ALL & ~E_NOTICE & ~E_DEPRECATED

display_errors
:   Ce paramètre permet d'activer ou non l'affichage des erreurs PHP dans les réponses émises au client. En production, il est recommandé de désactiver le display_errors.  
Par défaut ce paramètre est à “On”.

    [ini]
    display_errors = Off

## PostgreSQL {#pre-req-postgresql}

Dynacase Platform nécessite PostgreSQL version **9.1.**
La version 8.4 est aussi supportée mais pour des raisons de performances, la version 9.1 est fortement recommandée.

## HTTPD Apache

Dynacase Platform nécessite le serveur HTTPD Apache en version **2.2**

### Modules Apache

Les modules Apache suivants sont requis :

* php5_module (Voir [les pré-requis php](#pre-req-php))
* env_module
* expires_module
* dir_module
* auth_basic_module
* authn_file_module
* authz_host_module
* setenvif_module
* rewrite_module
