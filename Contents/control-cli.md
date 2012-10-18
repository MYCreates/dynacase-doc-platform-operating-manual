# Dynacase Control en mode CLI

Le mode CLI de dynacase-control permet d'administrer les contextes Dynacase en ligne de commande.

## Utilisation

La commande `wiff` permet de manipuler et administrer les contextes Dynacase. Les opérations actuellement accessibles à travers l'UI Web, seront accessibles en ligne de commande à l'aide de cette commande `wiff`. Cette commande doit être lancée sous vce les privilèges root. Pour cela, vous pouvez soit vous logger sous le compte root, soit utiliser `sudo` pour l'exécuter (Dans ce cas, vous pouvez également autoriser la commande wiff sans mot de passe en éditant le fichier [sudoers](http://www.manpagez.com/man/5/sudoers/)).

Si vous n'êtes pas 'root' sur le serveur, et que les opérations d'administrations s'effectuent avec 'sudo' :

    sudo /var/www/dynacase-control/wiff help
    Password: ******
    [message d'aide]

Si vous avez accès au compte 'root' sur le serveur :

    /var/www/dynacase-control/wiff help
    [message d'aide]

### Commandes

#### Lister les contextes existants

Cette opération permet d'afficher les contextes installés.

Syntaxe :

    list context [--pretty]

Exemple :

    /var/www/dynacase-control/wiff list context
    developpement
    test
    production

    /var/www/dynacase-control/wiff list context --pretty
    Name               Description                                                     
    -----------------------------------------------------------------------------------
    developpement      Développement
    test               Test
    production         Production

#### Manipuler les modules du contexte

##### Lister les modules installés

Cette opération affiche les modules installés dans le contexte sélectionné.

Syntaxe :

    context <context-name> module list installed [--pretty]

Exemple :

    /var/www/dynacase-control/wiff context test module list installed
    dynacase-fckeditor (2.6.3-7)
    dynacase-webdesk (1.2.2-1)
    dynacase-extjs (3.1.1-8)
    dynacase-toolbox (3.0.11-1)
    dynacase-extui (0.6.4-1)
    dynacase-workspace (0.6.1-1)
    dynacase-ecm (0.3.0-1)

##### Lister les modules disponibles

Cette opération affiche la liste des modules disponibles sur les dépôt de paquets accessibles par HTTP ou FTP.

Syntaxe :

    context <context-name> module list available [--pretty]` 

Exemple :

    /var/www/dynacase-control/wiff context test module list available
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

##### Lister les modules avec mise à jour proposée

Cette opération permet d'afficher la liste des modules installés dont une mise-à-jour est disponible sur les dépôts de paquets.

Syntaxe :

    context <context-name> module list upgrade [--pretty]

Exemple :

    /var/www/dynacase-control/wiff context test module list upgrade
    dynacase-platform (2.14.1-12)

##### Installer un module

Cette opération permet d'installer un module contenu dans un paquet local (archive .webinst stocké en local sur le serveur) ou à partir d'un paquet disponible sur un dépôt de paquets distant accessible par HTTP ou FTP.

Syntaxe : 

    context <context-name> module install [options] <localPackagePath>
    context <context-name> module install [options] <moduleName>

Les options disponibles sont :

* `--nopre` permet de ne pas exécuter les processus de 'pre-install' déclarés par le paquet.
* `--nopost` permet de ne pas exécuter les processus de 'post-install' déclarés par le paquet.
* `--nothing` permet de ne pas exécuter les processus de 'pre-install' et de 'post-install' déclarés par le paquet.
* `--force` permet de forcer l'installation du paquet même si celui-ci est déjà installé.

Exemple :

Installation d'un paquet distant :

    /var/www/dynacase-control/wiff context test module install dynacase-platform
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

Installation d'un paquet local :

    /var/www/dynacase-control/wiff context test module install /tmp/foo-1.2.3-4.webinst
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

##### Upgrader un module

Cette opération permet d'upgrader un module à partir d'un paquet local (archive .webinst stocké en local sur le serveur) ou à partir d'un paquet disponible sur un dépôt de paquets distant accessible par HTTP ou FTP.

Syntaxe :

    context <context-name> module upgrade [options] <localPackagePath>
    context <context-name> module upgrade [options] <moduleName>

Les options disponibles sont :

* `--nopre` permet de ne pas exécuter les processus de 'pre-upgrade' déclarés par le paquet.
* `--nopost` permet de ne pas exécuter les processus de 'post-upgrade' déclarés par le paquet.
* `--nothing` permet de ne pas exécuter les processus de 'pre-upgrade' et de 'post-install' déclarés par le paquet.
* `--force` permet de forcer l'upgrade du paquet même si une version supérieure ou égale est déjà installée.

Exemple :

    /var/www/dynacase-control/wiff context test module upgrade /tmp/foo-2.0.0-1.webinst
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

#### Manipuler les paramètres

##### Afficher la liste des paramètres d'un module

Cette opération permet d'afficher la liste des paramètres des modules installés.

Syntaxe :

    context <context-name> param show [<moduleName>]

Exemple :

    /var/www/dynacase-control/wiff context test param show 
    dynacase-platform:client_name = Test
    dynacase-platform:core_db = test
    dynacase-platform:authtype = html
    dynacase-platform:apacheuser = _www
    foo:bar = baz
    foo:bir = biz
    
    /var/www/dynacase-control/wiff context test param show foo
    foo:bar = baz
    foo:bir = biz

##### Afficher la valeur d'un paramètre d'un module

Cette opération permet d'afficher la valeur d'un paramètre d'un module donné.

Syntaxe :

    context <context-name> param get <module-name>:<param-name>

Exemple :

    /var/www/dynacase-control/wiff context test param get foo:bar
    foo:bar = baz

##### Positionner la valeur d'un paramètre d'un module

Cette opération permet de positionner la valeur d'un paramètre d'un module donné.

Syntaxe :

    context <context-name> param set <module-name>:<param-name> <param-value>

Exemple :

    /var/www/dynacase-control/wiff context <context-name> param set foo:bar buzz
    foo:bar = buzz

#### Passer un contexte en maintenance

Cette opération permet d'exécuter le script historique 'wstop' sur un contexte.
Dans ce mode, seul l'utilisateur master default a accès au contexte.

Syntaxe :

    wstop <context-name>

Exemple :

    /var/www/dynacase-control/wiff wstop test
    check update needed (/var/www/test/wcheck)

#### Mettre en service un contexte

Cette opération permet d'exécuter le script historique 'wstart' sur un contexte.

Syntaxe :

    wstart <context-name>

Exemple :
    
    /var/www/dynacase-control/wiff wstart test

#### Réinitialiser les catalogues de langue

Cette opération permet d'exécuter le script historique 'whattext' sur un contexte.

Syntaxe :

    whattext <context-name>

Exemple :

    /var/www/dynacase-control/wiff whattext test
    Administration système

#### Ouvrir un shell sous l'uid Apache

Cette opération permet d'ouvrir un shell, ou d'exécuter une commande, sous l'uid de l'utilisateur exécutant le serveur Apache afin d'effectuer manuellement des opérations d'administration spécifiques.

Syntaxe :

    context <context-name> shell

    context <context-name> exec  <command> [<command-options>]

Par défaut, si aucune commande n'est spécifiée, le shell par défaut définit pour l'utilisateur du serveur Apache est lancé. Si l'utilisateur n'a pas de shell associé, il faudra alors spécifier le chemin du shell qu'on souhaite exécuter avec la variante 'exec'.
Lors de l'ouverture du shell, ou de l'exécution de la commande, les variables d'environnement suivantes sont pré-positionnés :

* 'HOME' : le répertoire racine du contexte.
* 'wpub' : le répertoire racine du contexte (pour compatibilité scripts post/migr de Dynacase).
* 'pgservice_core' : le service Postgresql de la base 'core' (pour compatibilité scripts post/migr de Dynacase).
* 'pgservice_freedom' : le service Postgresql de la base 'freedom' (pour compatibilité scripts post/migr de Dynacase).
* 'freedom_context' : vaut toujours ”default” (pour compatibilité scripts post/migr de Dynacase).

Exemple :

    /var/www/dynacase-control/wiff context test shell /bin/bash
    wiff(test)~$ id
    uid=70(_www) gid=70(_www) egid=20(staff) groups=70(_www)
    wiff(test)~$ pwd
    /var/www/test
    wiff(test)~$ ./FOO/FOO_post U
    [...]
    /var/www/dynacase-control/wiff context test exec /usr/bin/tar -zcf /tmp/archive.tar.gz .
