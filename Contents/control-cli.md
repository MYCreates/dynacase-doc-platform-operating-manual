# Dynacase Control en mode CLI {#manex-ref:59a0440c-da01-47f9-81a4-76b6953fbcbb}

Le mode CLI de dynacase-control permet d'administrer les contextes Dynacase en ligne de commande.

## Utilisation {#manex-ref:9a04eacf-fe22-4761-8535-88da83bdccb5}

La commande `wiff` permet de manipuler et administrer les contextes Dynacase.
Les opérations actuellement accessibles à travers l'UI Web, seront accessibles
en ligne de commande à l'aide de cette commande `wiff`. Cette commande doit
être lancée sous `root`. Pour cela, vous pouvez soit vous logger sous le compte
`root`, soit utiliser `sudo` pour l'exécuter (Dans ce cas, vous pouvez
également autoriser la commande wiff sans mot de passe en éditant le fichier
[sudoers](http://www.manpagez.com/man/5/sudoers/)).

Si vous n'êtes pas `root` sur le serveur, et que les opérations
d'administrations s'effectuent avec `sudo` :

    $ sudo /var/www/dynacase-control/wiff help
    Password: ******
    [message d'aide]

Si vous avez accès au compte `root` sur le serveur :

    # /var/www/dynacase-control/wiff help
    [message d'aide]

Le CLI utilise un verrou afin de se prémunir contre l'exécution concurrente de
plusieurs opérations de modification (voir détails
[Opérations verrouillées][locked-op] ci-dessous).

## Commandes {#manex-ref:1a7837eb-e659-48c8-8635-afbeee899d79}

### Lister les contextes existants {#manex-ref:b2e5922d-7e9d-4755-b7ca-974b3f658937}

Cette opération permet d'afficher les contextes installés.

Syntaxe :

    list context [--pretty]

Exemple :

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

### Créer un nouveau contexte {#manex-ref:e1011c80-4563-4df0-858a-29f49e6582c6}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de créer un nouveau contexte.

Syntaxe :

    create context <context-name> <context-root>

Exemple :

    # /var/www/dynacase-control/wiff create context test /var/www/test

### Manipuler les propriétés d'un contexte {#manex-ref:565ae938-49ca-403d-8fa7-f7109463601b}

<span class="flag inline release from">control 1.5</span>

Les propriétés d'un contexte sont :

`url`
:   L'URL affichée sur le contexte.

`description`
:   La description affichée sur le contexte.

#### Voir la liste des propriétés d'un contexte {#manex-ref:9a3e6413-37f6-4f3e-ad50-8cb68e12adf7}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de voir la liste des propriétés avec leur valeur.

Syntaxe :

    context <context-name> property show

Exemple :

    # /var/www/dynacase-control/wiff context test property show
    url = /test/admin.php
    description = Contexte de test.

#### Récupérer la valeur d'une propriété d'un contexte {#manex-ref:aa5f69b0-b2de-4d0c-97bb-f88c3c98e29c}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de voir la valeur d'un propriété.

Syntaxe :

    context <context-name> property get <property-name>

Exemple :

    # /var/www/dynacase-control/wiff test property get url
    url = /test/admin.php

#### Modifier les propriétés d'un contexte {#manex-ref:7e963b20-c90b-4dfc-bcdd-eea55c189ead}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de modifier les propriétés d'un contexte.

Syntaxe :

    context <context-name> property set <property-name> <property-value>

Exemple :

    # /var/www/dynacase-control/wiff context test property set url http://www.example.net/test/
    # /var/www/dynacase-control/wiff context test property set description "Ceci n'est pas un contexte de test."

### Modifier les dépôts de paquets utilisés par un contexte {#manex-ref:c1a293b8-a043-44da-b2c4-2cc1c70375fa}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de voir/modifier la liste des dépôts de paquets utilisés
par un contexte.

Les dépôts de paquets utilisables par un contexte doivent être déclarés au
préalable dans dynacase-control (voir
[Gérer la liste des dépôts de paquets de dynacase-control][repository]
ci-dessous).

#### Voir les dépôts actifs sur un contexte {#manex-ref:e585dfac-d9ce-49fc-a663-5629489233e2}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    context <context-name> repository list

Exemple :

    # /var/www/dynacase-control/wiff context test repository list
    dynacase
    acme

#### Activer un dépôt sur un contexte {#manex-ref:559ac420-64bb-433f-87c1-0779ee26b604}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    context <context-name> repository enable <repo-name>

Exemple :

    # /var/www/dynacase-control/wiff context test repository enable foo

#### Désactiver un dépôt sur un contexte {#manex-ref:cf282c49-b4bb-4348-8616-34fd3d5b8f88}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    context <context-name> repository disable <repo-name>

Exemple :

    # /var/www/dynacase-control/wiff context test repository disable foo

#### Désactiver tous les dépôts sur un contexte {#manex-ref:f744b2e2-8fe2-4fae-84aa-a75843515fc2}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    context <context-name> repository disable --all

Exemple :

    # /var/www/dynacase-control/wiff context test repository disable --all

### Supprimer un contexte {#manex-ref:186bb0a7-f7b2-49da-9a84-9af5a0a5d306}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    delete context [options] <context-name>

Les options disponibles sont :

* `--nopre` permet de ne pas exécuter les processus de 'pre-delete' déclarés par les paquets.
* `--force` permet de ne pas tenir compte des statuts d'échec des processus de 'pre-delete'.

Exemple :

    # /var/www/dynacase-control/wiff delete context --force test

### Manipuler les modules du contexte {#manex-ref:6ba2cf7d-38d9-4237-b5a3-6dd64207891b}

#### Lister les modules installés {#manex-ref:1e33d831-1517-4455-a12e-43906759efea}

Cette opération affiche les modules installés dans le contexte sélectionné.

Syntaxe :

    context <context-name> module list installed [--pretty]

Exemple :

    # var/www/dynacase-control/wiff context test module list installed
    dynacase-fckeditor (2.6.3-7)
    dynacase-webdesk (1.2.2-1)
    dynacase-extjs (3.1.1-8)
    dynacase-core (3.2.5-1)
    dynacase-extui (0.6.4-1)
    dynacase-workspace (0.6.1-1)
    dynacase-ecm (0.3.0-1)

#### Lister les modules disponibles {#manex-ref:6c727aee-98da-4dde-8918-871a55c7b742}

Cette opération affiche la liste des modules disponibles sur les dépôt de paquets accessibles par HTTP ou FTP.

Syntaxe :

    context <context-name> module list available [--pretty]` 

Exemple :

    # /var/www/dynacase-control/wiff context test module list available
    dynacase-extjs (3.1.1-8)
    dynacase-core (3.2.5-1)
    dynacase-fckeditor (2.6.3-7)
    dynacase-freeevent (2.6.0-0)
    dynacase-webdesk (1.2.2-1)
    [...]
    dynacase-apisamples (0.1.3-1)
    dynacase-familysamples (0.1.0-2)
    dynacase-cas (0.0.2-1)
    dynacase-theme (0.0.1-4)
    dynacase-url (0.0.0-3)

#### Lister les modules avec mise à jour proposée {#manex-ref:1e9379b9-0601-4535-bb13-f7213adbadf5}

Cette opération permet d'afficher la liste des modules installés dont une mise-à-jour est disponible sur les dépôts de paquets.

Syntaxe :

    context <context-name> module list upgrade [--pretty]

Exemple :

    # /var/www/dynacase-control/wiff context test module list upgrade
    dynacase-core (3.2.6-1)

#### Installer un module {#manex-ref:bec588d2-4e07-4f3f-897f-6958d35e95f9}

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

    # /var/www/dynacase-control/wiff context test module install dynacase-core
    Will (i)nstall, (u)pgrade, or (r)eplace the following modules:
    - dynacase-core-3.2.5-1.20130221.090413 (i)
    - dynacase-jquery-installer-1.7.2-1.20120730.134605 (i)
    - dynacase-jquery-ui-installer-1.8.21-2.20120817.154930 (i)
    - dynacase-json2-1.0.0-0.20130214.114647 (i)
    - dynacase-datajs-3.2.5-0.20130220.144720 (i)
    - dynacase-jquery-dataTables-installer-1.9.1-0.20120622.121419 (i)
    - dynacase-ckeditor-installer-4.0.1-0.20130213.174659 (i)
    Proceed with installation ? [Y/n]
    
    Processing module 'dynacase-core' (3.2.5-1.20130221.090413) for install.
    Downloading module 'dynacase-core-3.2.5-1.20130221.090413'... [OK]
    [...]
    Done.

Installation d'un paquet local :

    # /var/www/dynacase-control/wiff context test module install /tmp/foo-1.2.3-4.webinst
    Processing required module 'foo' (1.2.3-4) for install.
    [...]
    Done.

Notes :

* <span class="flag inline release from">control 1.5</span> À partir de la
version `1.5` de dynacase-control, le format XML des modules est vérifié et un
avertissement est affiché si le format n'est pas conforme. Voir [Format des
modules webinst][module] et [Changements par rapport à l'ancien format
][module-changes] pour les changements à apporter à vos fichier `info.xml` de
description de module.

#### Upgrader un module {#manex-ref:a0618428-8ffe-41fe-9b4d-f29ae5400d6d}

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

    # /var/www/dynacase-control/wiff context test module upgrade /tmp/foo-2.0.0-1.webinst
    Processing required module 'foo' (2.0.0-1) for upgrade.
    [...]
    Done.

Notes :

* <span class="flag inline release from">control 1.5</span> À partir de la
version `1.5` de dynacase-control, le format XML des modules est vérifié et un
avertissement est affiché si le format n'est pas conforme. Voir [Format des
modules webinst][module] et [Changements par rapport à l'ancien format
][module-changes] pour les changements à apporter à vos fichier `info.xml` de
description de module.

### Archives de contextes {#manex-ref:fdb27535-7e93-4d14-927f-5c8d88fcd4f9}

#### Archiver un contexte {#manex-ref:6dd36914-0dd0-43fd-bfde-f90174642f2a}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    context <context-name> archive <archive-name> [--without-vault] [--description=<description>]

Les options disponibles sont :

* `--without-vault` permet de ne pas inclure l'archivage du vault dans
  l'archive du contexte.
* `--description=<description>` permet de saisir le champ de description de
  l'archive.

Exemple :

    # /var/www/dynacase-control/wiff context dev archive test
    test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5

#### Lister les archives {#manex-ref:91f85936-80db-4358-98a5-84c6dd5c7833}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    list archive

Exemple :

    # /var/www/dynacase-control/wiff list archive
    test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5
    pre-prod-838ac13e91d439d5c3a8bed86ea8adfee800c949

#### Voir les informations d'une archive {#manex-ref:6f6239c7-8f75-4932-8dc0-31bc99c1bad2}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    archive <archive-id> info

Exemple :

    # /var/www/dynacase-control/wiff archive test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5 info
    Archive 'test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5'
    -------------------------------------------------------
    
    id          = test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5
    date        = 2014-12-15 09:03:04
    name        = test
    size        = 42.121 Mo
    vault       = Yes
    description = dev@2014-12-15T09:03:04
    
    Installed modules:
    - dynacase-core (3.2.17-1)
    [...]

#### Restaurer l'archive d'un contexte {#manex-ref:74fa0502-c504-4ed0-8614-dccf6648c094}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    archive <archive-id> restore <context-name> <context-root> <pg-service-name> <vault-root>
                                   [--remove-profiles --user-login=<login> --user-password=<password>]
                                   [--clean-tmp-directory=<'yes'|'no'>]

Les options disponibles sont :

* `--remove-profiles` permet de donner tous les privilèges sur tous les
  documents à un nouvel utilisateur
  lors de la restauration du contexte (à utiliser en conjonction avec
  `--user-login` et `--user-password`).
* `--user-login` et `--user-password` permet de spécifier le login et le mot de
  passe du nouvel utilisateur auquel sont donnés tous les privilèges sur tous
  les documents.
* `--clean-tmp-directory=<'yes'|'no'>` permet de supprimer si les fichiers
  temporaires de la restauration sont effacés (`yes` ou comportement par
  défaut) ou s'il sont conservés (`no`).

Exemple :

    # /var/www/dynacase-control/wiff archive test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5 restore test2 /var/www/test2 db-test-2 /var/www/test2/vaultfs
    Context 'test2' successfully created from archive 'test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5'.

### Supprimer une archive de contexte {#manex-ref:cff48830-cf99-4698-903d-69ab3ede7b2d}

Syntaxe :

    delete <archive-id>

Exemple :

    # /var/www/dynacase-control/wiff delete archive test-1f84e69f127a5fb3f2d920c35beb12f2f2a6c4d5

### Manipuler les paramètres des modules {#manex-ref:f928d938-318c-49f5-ac4a-89f097bdb238}

#### Afficher la liste des paramètres d'un module {#manex-ref:9ba44c49-5b81-4dcb-80e0-b262a46a2a03}

Cette opération permet d'afficher la liste des paramètres des modules installés.

Syntaxe :

    context <context-name> param show [<moduleName>]

Exemple :

    # /var/www/dynacase-control/wiff context test param show
    dynacase-core:client_name = Test
    dynacase-core:core_db = test
    dynacase-core:authtype = html
    dynacase-core:lcdate = iso : standard format 8601
    dynacase-core:core_tmpdir = ./var/tmp
    dynacase-core:core_admin_passwd =
    dynacase-core:mod_deflate = yes
    foo:bar = baz
    foo:bir = biz
    
    # /var/www/dynacase-control/wiff context test param show foo
    foo:bar = baz
    foo:bir = biz

#### Afficher la valeur d'un paramètre d'un module {#manex-ref:5c66ddac-7057-4732-a34f-afc71e7638ca}

Cette opération permet d'afficher la valeur d'un paramètre d'un module donné.

Syntaxe :

    context <context-name> param get <module-name>:<param-name>

Exemple :

    # /var/www/dynacase-control/wiff context test param get foo:bar
    foo:bar = baz

#### Positionner la valeur d'un paramètre d'un module {#manex-ref:3dcef13c-6bd3-44d0-a9c6-eb87f36e7652}

Cette opération permet de positionner la valeur d'un paramètre d'un module donné.

Syntaxe :

    context <context-name> param set <module-name>:<param-name> <param-value>

Exemple :

    # /var/www/dynacase-control/wiff context <context-name> param set foo:bar buzz
    foo:bar = buzz

### Gérer la liste des dépôts de paquets de dynacase-control {#manex-ref:a07e974e-bc71-498a-97ca-9b4f7dcf4c1e}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de gérer la liste de dépôts de paquets connus par
dynacase-control.

#### Afficher la liste des dépôts de paquets {#manex-ref:278a3494-e656-4d2d-abef-f7444f554d9f}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    repository list

Exemple :

    # /var/www/dynacase-control/wiff repository list
    dynacase	http://dynacase.anakeen.com/3.2/webinst/
    local	http://localhost/repo/

#### Ajouter un dépôt de paquets {#manex-ref:efd1f9a5-b840-4d0c-a2da-5603618122d4}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    repository add <repo-name> <repo-url> [<repo-username> [<repo-password>]]

Exemple :

    # /var/www/dynacase-control/wiff repository add foo "http://example.net/foo/"
    # /var/www/dynacase-control/wiff repository add acme "http://example.net/acme/" "john.doe" "s3cr3t"

Notes :

* Le nom du dépôt ne peut contenir que des caractères alpha-numériques non
accentués et le symbole `_` (underscore). REGEX pour la validation du nom du
dépôt : `/^[a-zA-Z0-9_]+$/`.

#### Supprimer un dépôt de paquets {#manex-ref:1ce2468d-799d-44bb-9738-cd9a4141c1dc}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    repository delete <repo-name>

Exemple :

    # /var/www/dynacase-control/wiff repository delete foo

Notes :

* La suppression d'un dépôt de dynacase-control ne désactive pas son
utilisation par un contexte. Voir
[`Désactiver un dépôt sur un contexte`][repository-disable]. 

#### Supprimer tous les dépôts de paquets {#manex-ref:923e396e-245f-4288-9caa-97a6671d2ee8}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    repository delete --all

Exemple :

    # /var/www/dynacase-control/wiff repository delete --all

Notes :

* La suppression d'un dépôt de dynacase-control ne désactive pas son
utilisation par un contexte. Voir
[`Désactiver un dépôt sur un contexte`][repository-disable]. 

### Gérer les paramètres de dynacase-control {#manex-ref:ec9ba402-bf8a-42e3-bd61-2899bc583c87}

<span class="flag inline release from">control 1.5</span>

Cette opération permet de voir/modifier les paramètres de dynacase-control.

#### Liste des paramètres de dynacase-control {#manex-ref:02e03661-d67a-4b5a-94c9-389eb42eaf3c}

`wiff-update-host`
:   Base de l'URL de recherche des mises à jour de dynacase-control.

`wiff-update-path`
:   Chemin pour la recherche des mises à jour de dynacase-control.

`wiff-update-file`
:   Nom du fichier de la dernière version de dynacase-control.

`wiff-update-login` (optionnel)
:   Nom d'utilisateur pour la recherche des mises à jour (si authentification
    HTTP Basic requise).

`wiff-update-password` (optionnel)
:   Mot de passe pour la recherche des mises à jour (si authentification HTTP
    Basic requise).

`use-proxy` (optionnel)
:   Active l'utilisation d'un proxy HTTP pour les requêtes HTTP/FTP émises par
    dynacase-control.
    
    Valeurs : `yes` ou `no`.

`proxy-host` (optionnel)
:   Nom d'hôte du proxy HTTP à utiliser si `use-proxy` est activé.
    
    Ex. `proxy.example.net`

`proxy-port` (optionnel)
:   Port du proxy HTTP à utiliser si `use-proxy` est activé.
    
    Ex. : `3128`

`proxy-username` (optionnel)
:   Nom d'utilisateur pour la connexion au proxy HTTP `proxy-host`.

`proxy-password` (optionnel)
:   Mot de passe pour la connexion au proxy HTTP `proxy-host`.

`auto-configuration-sender-interval`
:   Intervalle (en nombre de jours) pour la soumission des information des
    contextes avec [`send_configuration`][send_configuration] lorsque ceux-ci
    sont enregistrés avec le compte EEC.
    
    Valeurs : entier de `1` à `31`.

`local-log` <span class="flag inline release from">control 1.5</span>
:   Active la journalisation des messages d'erreurs dans le fichier
    `log/wiff.log`.
    
    Valeurs : `yes` ou `no`.

`syslog-facility` <span class="flag inline release from">control 1.5</span>
:   Facility syslog utilisée par les messages d'erreurs émis par
    dynacase-control.
    
    Voir [`Paramétrage syslog`][param-syslog].

#### Voir les paramètres de dynacase-control {#manex-ref:1ff2e738-f0a9-41d1-9eae-99cd6d71a849}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    param show

Exemple :

    # /var/www/dynacase-control/wiff param show
    wiff-update-host = http://dynacase.anakeen.com/ (visible)
    wiff-update-path = /control/ (visible)
    wiff-update-file = dynacase-control-current.tar.gz (visible)
    wiff-update-login =  (visible)
    wiff-update-password =  (visible)
    use-proxy = no (visible)
    proxy-host =  (visible)
    proxy-port =  (visible)
    proxy-username =  (visible)
    proxy-password =  (visible)
    auto-configuration-sender-interval = 30 (visible)
    local-log = yes (hidden)
    syslog-facility = LOG_USER (hidden)

#### Récupérer la valeur d'un paramètre de dynacase-control {#manex-ref:81764349-fcb2-4d8d-bd9a-64ab221d7d75}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    param get <param-name>

Exemple :

    # /var/www/dynacase-control/wiff param get local-log
    local-log = yes

#### Modifier la valeur d'un paramètre de dynacase-control {#manex-ref:b115dc19-7b70-4dcc-9fa2-1f869b9b374b}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    param set <param-name> <param-value> ['hidden']

Exemple :

    # /var/www/dynacase-control/wiff param set local-log no

Notes :

* La visibilité `hidden` permet de masquer la présentation du paramètre dans
l'interface Web.

### Enregistrer dynacase-control avec son compte EEC {#manex-ref:15a76a3f-186c-4af9-9daf-75dc252d9235}

<span class="flag inline release from">control 1.5</span>

Cette opération permet d'enregistrer dynacase-control avec son compte EEC.

#### Voir le status d'enregistrement {#manex-ref:d27303d8-b7c8-49a1-b0d1-db0d96a2caef}

Syntaxe :

    register

Exemple :

    # /var/www/dynacase-control/wiff register
    Not registered.

#### Enregistrer dynacase-control avec son compte EEC {#manex-ref:179ca655-4b0f-47c1-9196-6302ee50a8b9}

<span class="flag inline release from">control 1.5</span>

Syntaxe :

    register <eec-username> <eec-password>

Exemple :

    # /var/www/dynacase-control/wiff register "john.doe" "s3cr3t"
    Successfully registered dynacase-control.
    
    # /var/www/dynacase-control/wiff register
    Registered with EEC username 'john.doe'.

### Enregistrer un contexte avec son compte EEC {#manex-ref:f1983ab4-4a45-4f0a-91b8-c903e02c89ac}

<span class="flag inline release from">control 1.5</span>

Cette opération permet d'enregistrer un contexte avec son compte EEC.

Syntaxe :

    context <context-name> register

Exemple :

    # /var/www/dynacase-control/wiff context test register

Notes :

* L'enregistrement d'un contexte avec son compte EEC nécessite au préalable
que dynacase-control ait été enregistré avec un compte EEC.
Voir [`Enregistrer dynacase-control avec son compte EEC`][wiff-register].

### Envoi de la configuration au compte EEC {#manex-ref:39a09bb4-8281-46f9-9699-5263a0772d44}

Cette opération permet d'envoyer la configuration de tous les contextes
enregistrés avec le compte EEC.

Syntaxe :

    send_configuration

Exemple :

    # /var/www/dynacase-control/wiff send_configuration

### Passer un contexte en maintenance {#manex-ref:b7fac359-18af-42f6-a414-32beaf02666c}

Cette opération permet d'exécuter le script historique 'wstop' sur un contexte.
Dans ce mode, seul l'utilisateur master default a accès au contexte.

Syntaxe :

    wstop <context-name>

Exemple :

    # /var/www/dynacase-control/wiff wstop test

### Mettre en service un contexte {#manex-ref:03198250-37bf-40d2-bc98-036098fa29a3}

Cette opération permet d'exécuter le script historique 'wstart' sur un contexte.

Syntaxe :

    wstart <context-name>

Exemple :
    
    # /var/www/dynacase-control/wiff wstart test

### Réinitialiser les catalogues de langue {#manex-ref:d9a9e44b-5076-4a35-8c64-9e860e97a360}

Cette opération permet d'exécuter le script historique 'whattext' sur un contexte.

Syntaxe :

    whattext <context-name>

Exemple :

    # /var/www/dynacase-control/wiff whattext test

### Ouvrir un shell sous l'uid Apache {#manex-ref:a122eb25-779a-4379-aa91-ffa1bb90daf7}

Cette opération permet d'ouvrir un shell, ou d'exécuter une commande, sous l'uid de l'utilisateur exécutant le serveur Apache afin d'effectuer manuellement des opérations d'administration spécifiques.

Syntaxe :

    context <context-name> shell

    context <context-name> exec  <command> [<command-options>]

Par défaut, si aucune commande n'est spécifiée, le shell par défaut définit pour l'utilisateur du serveur Apache est lancé. Si l'utilisateur n'a pas de shell associé, il faudra alors spécifier le chemin du shell qu'on souhaite exécuter avec la variante 'exec'.
Lors de l'ouverture du shell, ou de l'exécution de la commande, les variables d'environnement suivantes sont pré-positionnés :

* `HOME` : le répertoire racine du contexte.
* `wpub` : le répertoire racine du contexte (pour compatibilité scripts post/migr de Dynacase).
* `pgservice_core` : le service Postgresql de la base 'core' (pour compatibilité scripts post/migr de Dynacase).
* `pgservice_freedom` : le service Postgresql de la base 'freedom' (pour compatibilité scripts post/migr de Dynacase).
* `freedom_context` : vaut toujours ”default” (pour compatibilité scripts post/migr de Dynacase).

Exemple :

    # /var/www/dynacase-control/wiff context test shell /bin/bash
    $ id
    uid=70(_www) gid=70(_www) egid=20(staff) groups=70(_www)
    $ pwd
    /var/www/test
    $ ./FOO/FOO_post U
    [...]
    # /var/www/dynacase-control/wiff context test exec /usr/bin/tar -zcf /tmp/archive.tar.gz .

## Opérations verrouillées {#manex-ref:9565d571-b8b9-4bc2-8192-997772404b6c}

<span class="flag inline release from">control 1.5</span>

Les opérations suivantes sont soumises à l'obtention du verrou exclusif :

* [Créer un nouveau contexte][create-context]
* [Installer un module][module-install]
* [Upgrader un module][module-upgrade]
* [Activer un dépôt sur un contexte][context-repo-enable]
* [Désactiver un dépôt sur un contexte][context-repo-disable]
* [Désactiver tous les dépôts sur un contexte ][context-repo-disable-all]
* [Positionner la valeur d'un paramètre d'un module][module-param-set]
* [Modifier la valeur d'un paramètre de dynacase-control][wiff-param-set]
* [Ajouter un dépôt de paquets][wiff-repo-add]
* [Supprimer un dépôt de paquets][wiff-repo-del]
* [Supprimer tous les dépôts de paquets][wiff-repo-del-all]
* [Voir le status d'enregistrement][wiff-register]
* [Enregistrer dynacase-control avec son compte EEC][wiff-register-auth]
* [Enregistrer un contexte avec son compte EEC][context-register]
* [Restaurer l'archive d'un contexte][archive_restore]
* [Archiver un contexte][context_archive]
* [Supprimer une archive de contexte][delete_archive]

Le verrou est implémenté avec un appel système `flock` sur le fichier
`conf/contexts.xml.lock`.

Si le CLI ne peut obtenir un verrou exclusif, alors il se termine avec un exit
code `100` et affiche le message `Error locking Dynacase-Control: Already
locked by process with pid 'xxx'` si le verrou est pris par un autre CLI, ou
`Error locking Dynacase-Control: xxx` pour une erreur générale (e.g. problème
dans l'accès au fichier `conf/contexts.xml.lock`).

<!-- links -->
[locked-op]: #manex-ref:9565d571-b8b9-4bc2-8192-997772404b6c
[module-install]: #manex-ref:bec588d2-4e07-4f3f-897f-6958d35e95f9
[module-upgrade]: #manex-ref:a0618428-8ffe-41fe-9b4d-f29ae5400d6d
[module-param-set]: #manex-ref:3dcef13c-6bd3-44d0-a9c6-eb87f36e7652
[repository]: #manex-ref:a07e974e-bc71-498a-97ca-9b4f7dcf4c1e
[param-syslog]: #manex-ref:cedce829-1eb2-4610-bb0f-c0f8ad17cbeb
[send_configuration]: #manex-ref:39a09bb4-8281-46f9-9699-5263a0772d44
[repository-disable]: #manex-ref:cf282c49-b4bb-4348-8616-34fd3d5b8f88
[wiff-register]: #manex-ref:179ca655-4b0f-47c1-9196-6302ee50a8b9
[wiff-param-set]: #manex-ref:b115dc19-7b70-4dcc-9fa2-1f869b9b374b
[wiff-repo-add]: #manex-ref:efd1f9a5-b840-4d0c-a2da-5603618122d4
[wiff-repo-del]: #manex-ref:1ce2468d-799d-44bb-9738-cd9a4141c1dc
[wiff-repo-del-all]: #manex-ref:923e396e-245f-4288-9caa-97a6671d2ee8
[context-repo-enable]: #manex-ref:559ac420-64bb-433f-87c1-0779ee26b604
[context-repo-disable]: #manex-ref:cf282c49-b4bb-4348-8616-34fd3d5b8f88
[context-repo-disable-all]: #manex-ref:f744b2e2-8fe2-4fae-84aa-a75843515fc2
[wiff-register-auth]: #manex-ref:179ca655-4b0f-47c1-9196-6302ee50a8b9
[wiff-register]: #manex-ref:d27303d8-b7c8-49a1-b0d1-db0d96a2caef
[context-register]: #manex-ref:f1983ab4-4a45-4f0a-91b8-c903e02c89ac
[create-context]: #manex-ref:e1011c80-4563-4df0-858a-29f49e6582c6
[module-changes]: #manex-ref:8005debe-6796-4c93-bf66-78889b153bfb
[module]: #manex-ref:f28ae532-05cf-4a2d-a959-fbf258f1a778
[context_archive]: #manex-ref:6dd36914-0dd0-43fd-bfde-f90174642f2a
[list_archive]: #manex-ref:91f85936-80db-4358-98a5-84c6dd5c7833
[archive_info]: #manex-ref:6f6239c7-8f75-4932-8dc0-31bc99c1bad2
[archive_restore]: #manex-ref:74fa0502-c504-4ed0-8614-dccf6648c094
[delete_archive]: #manex-ref:cff48830-cf99-4698-903d-69ab3ede7b2d
