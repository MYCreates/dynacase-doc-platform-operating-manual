# Pré-requis {#manex-ref:7190c4ba-9c34-430d-9165-522c3ffe8eb9}


Ce chapitre identifie l'ensemble des pré-requis pour l'installation et
l'utilisation de Dynacase Platform.

Seuls les pré-requis pour les modules standards sont listés.


## Nouveautés 3.2 {#manex-ref:6b3e79e0-241c-4c6f-b7b4-3835d74d20c8}

### php-intl  {#manex-ref:45c0242a-5942-455f-935c-cdb78a19c6be}

Installer le module php `Intl` (`php5-intl` habituellement) et penser à
relancer le service apache.

Ce module est utilisé pour l'internationalisation.

### Implémentation libc de Iconv {#manex-ref:77dcc67e-dca0-43ae-a97c-9ec16d2510a4}

La fonction `iconv` doit être fournie par PHP compilé avec GLIBC.

Pour vérifier cela :  

    [bash]
    $ iconv --version
    iconv (Ubuntu EGLIBC 2.11.1-0ubuntu7.8) 2.11.1  
    ...
    $ php -r 'var_dump(ICONV_IMPL); var_dump(iconv("UTF-8", "ASCII//TRANSLIT", "\xc3\xa9t\xc3\xa8"));'  
    string(5) "glibc"
    string(3) "ete"   

### Locales `fr_FR` et `en_US` {#manex-ref:05bc59a4-8523-4cfc-be0b-8159b2b152f4}

Les locales `fr_FR.UTF-8` et `en_US.UTF-8` (ainsi que les dictionnaires aspell
de langue `fr` et `en`) doivent être installés et disponibles sur le serveur.

### Apache `mod_headers` {#manex-ref:78eeb11a-d460-4d4f-bcbf-d46b36c6e5fc}

Installer et activer le module Apache `mod_headers`.

## Poste client {#manex-ref:1636e34c-7f63-4cc7-84d2-721c68d69475} 

Les navigateurs supportés sont :

* Internet Explorer 8, 9 10 et 11 <span class="flag update">Updated</span> 
* Firefox &gt;= 4.0.1 <span class="flag update">Updated</span>
* Chrome branche stable


<span class="flag inline release from">3.2 R11</span> IE 8, 9, 10 et 11 /
Chrome stable / Firefox &gt;= 4.0.1

<span class="flag inline release until">3.2 R10</span> IE 8 et 9 / Chrome
stable / Firefox &gt; 3.6  

## Serveur {#manex-ref:30634c94-81c6-40e3-b45e-7c4df94baff4}

### GNU/Linux {#manex-ref:8ec0b71f-b127-45f9-9e63-b4b28ee26438}

Dynacase fonctionne sur un système GNU/Linux (Debian, Ubuntu, RedHat, etc.).

#### Commandes système {#manex-ref:e9b858db-a445-417b-b13c-d65c050e4f07}

Dynacase requiert les commandes système suivantes :

* `rm`
* `file`
* `mkdir`
* `tar`
* `gzip`
* `zip` (commande `zip` avec support de l'option `-Z store` de `info-zip` &gt;=
  3.0. En cas de problème avec la commande `zip`, utiliser la version fournie
  par [Info-ZIP](http://www.info-zip.org/))
* `unzip` (En cas de problème avec la commande `unzip`, utiliser la version
  fournie par [Info-ZIP](http://www.info-zip.org/))
* `dot` (fourni par [Graphviz](http://graphviz.org/))
* `convert` (fourni par [ImageMagick](http://www.imagemagick.org/))
* `recode`
* `html2ps`
* `ps2pdf` (fourni par [Ghostscript](http://www.ghostscript.com/))
* `php` (PHP en ligne de commande. Voir [les pré-requis php](#pre-req-php))
* `psql` (Voir [les pré-requis postgresql](#pre-req-postgresql))
* `pg_dump` (Voir [les pré-requis postgresql](#pre-req-postgresql))
* `msgcat` (fourni par
  [gettext](http://www.gnu.org/software/gettext/gettext.html))
* `ldapdelete` (fourni par [OpenLDAP](http://www.openldap.org/)) (*optionnel*)

#### Locales {#manex-ref:2a7386e4-4345-4549-afb5-9922503b0eee}

Dynacase requiert que les locales systèmes `fr_FR.UTF-8` et `en_US.UTF-8`
soient actives et correctement configurées sur le système.

Dynacase requiert aussi que les dictionnaires de langue `fr` et `en` de la
librairie `aspell` soient installés et accessibles pour l'extension `pspell` de
PHP.

### PHP {#pre-req-php}  

<span class="flag update">Updated</span> Dynacase Platform nécessite PHP en
version **5.6** :

* les versions PHP **5.4** et **5.6** ne seront plus supportées à compter de la
  prochaine release de maintenance (mai 2016)
* la version **5.6** est désormais supportée.

<span class="flag inline release from">3.2 R14</span> 5.4 / 5.5 / 5.6  
<span class="flag inline release from">3.2 R12</span> 5.4 / 5.5   
<span class="flag inline release from">3.2 R11</span> 5.4 / 5.5   
<span class="flag inline release obsolete">3.2 R11</span> 5.3  

__La version 5.3 n'est plus compatible à compter de la release 3.2 R12.__

#### Zend Server {#manex-ref:28510102-9e79-4551-8f5b-9b65251a3360}

Pour les distributions Linux qui ne fournissent pas la version PHP nécessaire,
[Zend Server](http://www.zend.com/en/products/server/) fournit différentes
versions de PHP pour les distributions Linux.

#### Extensions PHP {#manex-ref:e0d4bb4c-dd87-4eb4-9c32-703b9108f543}
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

#### Composants PEAR {#manex-ref:5987ddd7-fd7f-4bcd-8ad6-1d9cafadd8a6} 

<span class="flag update">Updated</span> modules Webdesk et Webdesk Services :
:  `XML_Parser` (1.3.2) / `XML_RSS` (1.0.2)

<span class="flag inline release until">3.2 R10</span>module Core :
:  `XML_Parser` (1.3.2) / `XML_RSS (1.0.2)` / `Net_SMTP` (1.6.0) / `Mail_Mime` (1.8.0) / `Crypt_CHAP` (optionnel)

#### Paramétrage PHP {#manex-ref:315e62bd-dbfd-4d33-a520-75b1f2c07522}

Certains paramètres de PHP doivent être modifiés afin que Dynacase Platform fonctionne au mieux et en fonction de votre utilisation. Ces valeurs préconisées doivent être revues en fonction de votre configuration réelle et de vos applications.

##### Paramètres INI {#manex-ref:04ee4723-a3c8-45c2-ad17-ddbb13e580f2}

`date.timezone`
:   Ce paramètre permet de spécifier le fuseau horaire utilisé par les
    fonctions de manipulation de date.
    
        [ini]
        date.timezone = 'Europe/Paris'

`max_execution_time`
:   Ce paramètre permet de spécifier le temps maximal (en seconde) de
    traitement d'une requête par PHP.  Par défaut ce paramètre est à “30”.
    
        [ini]
        max_execution_time = 300 ; 5 min.

`max_file_uploads`
:   Ce paramètre permet de spécifier le nombre maximum de fichiers qui seront
    pris en compte par PHP lors de la soumission d'un formulaire contenant des
    fichiers.
    
    Cette valeur doit être en cohérence avec le nombre maximum de fichier
    pouvant être soumis lors de l'enregistrement d'un document.
    
    Si ce n'est pas le cas, l'enregistrement du document est refusé à
    l'utilisateur et le message d'erreur suivant est présenté à l'utilisateur :
    "`Trop de fichiers dans le formulaire. Veuillez contacter votre
    administrateur système pour augmenter max_file_uploads dans php.ini. Le
    maximum est de %s`". 
    
    Par défaut ce paramètre est à “20”.
    
        [ini]
        max_file_uploads = 100

`max_input_vars`
:   Ce paramètre permet de spécifier le nombre maximum de variables de
    formulaires prises en compte par PHP lors de la soumission des formulaires.
    
    Si vous avez beaucoup d'attributs sur vos familles et que la valeur
    déclarée de ce paramètre est trop basse, le message d'erreur suivant est
    présenté à l'utilisateur lors de la soumission des formulaire d'édition de
    documents : "`Variables d'entrée dépassées %s. Veuillez contacter votre
    administrateur système pour augmenter max_input_vars dans php.ini.`".
    
    Par défaut ce paramètre est à "1000".
    
        [ini]
        max_input_vars = 1000

`upload_max_filesize`
:   Ce paramètre permet de spécifier la taille maximale d'un fichier téléversé.
    Si un fichier d'une taille supérieure est envoyé par le navigateur, il ne
    sera pas pris en compte par PHP.  
    
    Par défaut ce paramètre est à “2M” (2 Mo).
    
        [ini]
        upload_max_filesize = 20M

`max_post_size`
:   Ce paramètre permet de spécifier la taille maximale d'une requête de type
    `POST`.
    
    Généralement, il doit être supérieur à `upload_max_filesize` et être au
    plus égal à `max_file_uploads` * `upload_max_filesize` :
    `upload_max_filesize` &lt; `post_max_size` &lt;= ( `max_file_uploads` *
    `upload_max_filesize` )  
    
    Par défaut ce paramètre est à “8M” (8 Mo).
    
        [ini]
        post_max_size = 128M

`error_reporting`
:   Ce paramètre permet de spécifier le niveau de reporting des
    notices/warnings/erreurs/etc. Il est nécessaire de ne pas afficher les
    messages de notices (*`E_NOTICE`*), de dépréciation (*`E_DEPRECATED`*) et
    de suggestion (*`E_STRICT`*) de PHP lors de l'utilisation de Dynacase
    Platform en production.
    
        [ini]
        error_reporting = E_ALL & ~E_NOTICE & ~E_DEPRECATED & ~E_STRICT

`display_errors`
:   Ce paramètre permet d'activer ou non l'affichage des erreurs PHP dans les
    réponses émises au client. En production, il est recommandé de désactiver
    le `display_errors` (les messages d'erreur/warning/suggestion de PHP seront
    alors consultables sur le serveur dans le fichier spécifié par le paramètre
    INI `error_log`).
    
    Par défaut ce paramètre est à `On`.
    
        [ini]
        display_errors = Off

## PostgreSQL {#pre-req-postgresql} 

<span class="flag update">Updated</span> Dynacase nécessite PostgreSQL version
**9.4** :

* Les versions **9.1** et **9.2** de PostgreSQL ne seront plus supportées à
  compter de la release de maintenance publiée en mai 2016.
* La version **9.4** est désormais supportées.

<span class="flag inline release from">3.2 R14</span> 9.1 / 9.2 / 9.3 / 9.4  
<span class="flag inline release from">3.2 R12</span> 9.1 / 9.2 / 9.3  
<span class="flag inline release from">3.2 R11</span> 9.1 / 9.2 / 9.3  
<span class="flag inline release obsolete">3.2 R11</span> 8.4  

__La version 8.4 n'est plus compatible à compter de la release 3.2 R12__

**Les différentes optimisation, en particulier sur le calcul des droits ne sont
effectives qu'avec la version 9.1 de PostgreSQL.**

Dynacase utilise le fichier de service Postgresql (`pg_service.conf`) pour la
définition des paramètres de connexion à la base de données :

* [http://www.postgresql.org/docs/9.1/static/libpq-pgservice.html](http://www.postgresql.org/docs/9.1/static/libpq-pgservice.html)

Pour identifier l'emplacement du fichier `pg_service.conf` sur votre
distribution Linux, vous pouvez utiliser la commande suivante :

    # pg_config --sysconfdir

## HTTPD Apache {#manex-ref:89988168-0127-4782-9ec2-2d531fc28fd9}

Dynacase Platform nécessite le serveur HTTPD Apache en version **2.2** et **2.4**.

Le répertoire dans lequel sera installé Dynacase doit avoir un `AllowOverride
All` afin que les `.htaccess` livrée par Dynacase soient bien pris en compte
par Apache :

* [http://httpd.apache.org/docs/2.2/mod/core.html#allowoverride](http://httpd.apache.org/docs/2.2/mod/core.html#allowoverride)

L'utilisation avec Apache **2.4** requiert l'utilisation du module `mod_access_compat` :

* [https://httpd.apache.org/docs/2.4/fr/mod/mod\_access\_compat.html](https://httpd.apache.org/docs/2.4/fr/mod/mod_access_compat.html)

### Modules Apache {#manex-ref:995b8d07-3121-4775-b71e-7cf79f51fb44}

Les modules Apache suivants sont requis :

* `php5_module` (Voir [les pré-requis php](#pre-req-php))
* [`env_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_env.html)
* [`expires_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_expires.html)
* [`dir_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_dir.html)
* [`auth_basic_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_auth_basic.html)
* [`authn_file_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_authn_file.html)
* [`authz_host_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_authz_host.html)
* [`setenvif_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_setenvif.html)
* [`rewrite_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_rewrite.html)
* [`headers_module`](http://httpd.apache.org/docs/2.2/en/mod/mod_headers.html)

Si vous utilisez Apache 2.4, les modules suivants sont requis en plus des
modules ci-dessus :

* [`mod_access_compat`](https://httpd.apache.org/docs/2.4/fr/mod/mod_access_compat.html)


[^1]: pour compatibilité seulement.
[^2]: pour compatibilité seulement.
