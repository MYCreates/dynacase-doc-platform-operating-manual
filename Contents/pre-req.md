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

### Locales `fr_FR` et `en_US`

Les locales `fr_FR.UTF-8` et `en_US.UTF-8` (ainsi que les dictionnaires aspell
de langue `fr` et `en`) doivent être installés et disponibles sur le serveur.

### Apache mod_headers

Installer et activer le module Apache `mod_headers`.

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
* `zip` (commande `zip` avec support de l'option `-Z store` de info-zip >= 3.0. En cas de problème avec la commande `zip`, utiliser la version fournie par [Info-ZIP](http://www.info-zip.org/))
* `unzip` (En cas de problème avec la commande `unzip`, utiliser la version fournie par [Info-ZIP](http://www.info-zip.org/))
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

#### Locales

Dynacase requiert que les locales systèmes `fr_FR.UTF-8` et `en_US.UTF-8` soient
actives et correctement configurées sur le système.

Dynacase requiert aussi que les dictionnaires de langue `fr` et `en` de la
librairie `aspell` soient installés et accessibles pour l'extension pspell de
PHP.

### PHP {#pre-req-php}

Dynacase Platform nécessite PHP en version **5.4** (ou 5.3) :

* La version 5.4 est supportée officiellement.
* La version 5.3 est supportée pour compatibilité. Anakeen se réserve le droit de corriger d'éventuelles anomalies induites par cette version.

#### Zend Server

Pour les distributions Linux qui ne fournissent pas la version PHP nécessaire,
[Zend Server](http://www.zend.com/en/products/server/) fournit différentes
versions de PHP pour les distributions Linux.

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

Dynacase nécessite PostgreSQL version **9.1** (ou 8.4) :

* La version 9.1 est supportée officiellement.
* La version 8.4 est supportée pour compatibilité. Anakeen se réserve le droit
de corriger d'éventuelles anomalies induites par cette version.

**Les différentes optimisation, en particulier sur le calcul des droits ne sont
effectives qu'avec la version 9.1 de PostgreSQL.**

Dynacase utilise le fichier de service Postgresql (`pg_service.conf`) pour la définition des paramètres de connexion à la base de données :

* [http://www.postgresql.org/docs/9.1/static/libpq-pgservice.html](http://www.postgresql.org/docs/9.1/static/libpq-pgservice.html)

Pour identifier l'emplacement du fichier `pg_service.conf` sur votre distribution Linux, vous pouvez utiliser la commande suivante :

    # pg_config --sysconfdir

## HTTPD Apache

Dynacase Platform nécessite le serveur HTTPD Apache en version **2.2**

Le répertoire dans lequel sera installé Dynacase doit avoir un `AllowOverride All` afin que les `.htaccess` livrée par Dynacase soient bien pris en compte par Apache :

* [http://httpd.apache.org/docs/2.2/mod/core.html#allowoverride](http://httpd.apache.org/docs/2.2/mod/core.html#allowoverride)

### Modules Apache

Les modules Apache suivants sont requis :

* php5_module (Voir [les pré-requis php](#pre-req-php))
* [env_module](http://httpd.apache.org/docs/2.2/en/mod/mod_env.html)
* [expires_module](http://httpd.apache.org/docs/2.2/en/mod/mod_expires.html)
* [dir_module](http://httpd.apache.org/docs/2.2/en/mod/mod_dir.html)
* [auth_basic_module](http://httpd.apache.org/docs/2.2/en/mod/mod_auth_basic.html)
* [authn_file_module](http://httpd.apache.org/docs/2.2/en/mod/mod_authn_file.html)
* [authz_host_module](http://httpd.apache.org/docs/2.2/en/mod/mod_authz_host.html)
* [setenvif_module](http://httpd.apache.org/docs/2.2/en/mod/mod_setenvif.html)
* [rewrite_module](http://httpd.apache.org/docs/2.2/en/mod/mod_rewrite.html)
* [headers_module](http://httpd.apache.org/docs/2.2/en/mod/mod_headers.html)
