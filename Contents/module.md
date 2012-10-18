# Format des modules webinst {#module}

Dans ce paragraphe nous allons détailler les éléments constitutifs d'un module
webinst et mettre en œuvre ces éléments pour construire un module d'exemple
que nous nommerons *dynacase-foo*.

Pour bien suivre cette présentation, il est souhaitable d'avoir bien en tête
les notions d'Applications et d'Actions de Dynacase et le fonctionnement
général de ceux-ci.

## Fichier `info.xml`

Le fichier `info.xml` permet de décrire le module Dynacase en fournissant en
particulier :

* le nom du module,
* la version du module,
* une description,
* une liste de dépendances avec d'autres modules Dynacase,
* un ensemble d'action de pre-install, post-install,
* un ensemble de paramètres,
* un descriptif des évolutions (changelog)

### Exemple de fichier de info.xml

    [xml]
    <?xml version="1.0"?>
    <module name="dynacase-foo" version="1.2.3" release="1" [basecomponent="no"]>
      <description lang="en">dynacase foo</description>
      <description lang="fr">dynacase toto</description>

      <requires>
        <installer version="1.0.0" comp="ge" />
        <module name="dynacase-bar" />
        <module name="dynacase-baz" version="1.0.0" comp="ge" />
      </requires>

      <parameters>
        <param name="foo_dir" label="Directory of FOO" type="text" default="/var/foo" needed="yes" />
        <param name="foo_color" label="Color of FOO" type="enum" values="red|green|blue" default="green" needed="no" />
      </parameters>

      <changelog>
        <version number="1.1.1" date="2009-01-01">
          <change title="First change" url="http://dev.dynacase.org/issues/111">Comment for first change.</change>
          <change title="Second change">Comment for second change.</change>
          <change title="Third change"></change>
        </version>
        <version number="1.1.0" date="2008-12-15"/>
      </changelog>

      <pre-install>
        <check type="syscommand" command="zip" />
        <check type="phpfunction" function="pg_connect">
          <help>You might need to install a php-pg package.</help>
        </check>
        <check type="file" file="/var/foo" predicate="is_dir" />
      </pre-install>

      <post-install>
        <process command="programs/app_post FOO I" />
        <process command="programs/record_application FOO" />
        <process command="programs/app_post FOO U" />
        <process command="programs/update_catalog" />
      </post-install>

      <post-upgrade>
        <process command="programs/pre_migration FOO" />
        <process command="programs/app_post FOO U" />
        <process command="programs/record_application FOO" />
        <process command="programs/post_migration FOO" />
        <process command="programs/update_catalog" />
      </post-upgrade>

      <reconfigure>
        <process command="FOO/reconfigure_foo" />
      </reconfigure>

    </module>

## Description d'un module

### Préambule Module

La racine du document `info.xml` est un tag `<module>` avec les attributs suivants :

name
:   le nom du module

version
:   la version du module (sous la forme `N.N.N`)

release
:   le numéro de release de la version

basecomponent
:   `yes` ou `no`, permet de spécifier si le module est un module de base.
    Dans ce cas, son installation sera obligatoire.  
    *Optionnel*, par défaut : `no`

author
:   l'auteur du module (ex. `John Doe  <john.doe@example.net>`)  
    *optionnel*

licence
:   licence du module  
    *optionnel*
    <span class="fixme">préciser le fonctionnement (fichier , etc.)</span>

vendor
:   fournisseur du module (ex. `ACME Corp.`)  
    *optionnel*

Exemple :

    [xml]
    <?xml version="1.0"?>
    <module
        name="dynacase-foo"
        version="1.2.3"
        release="rc1"
        author="John Doe &lt;john.doe@example.net&gt;"
        licence="GPLV2"
        vendor="ACME Corp."
    >

    […]

    </module>

### Description

Le module peut fournir une description textuelle pour expliciter le rôle du module.
On pourra fournir des descriptions localisées en utilisant l'attribut `lang`.

Exemple :

    [xml]
    <description lang="fr">Ce module permet à Dynacase de se connecter à FOO</description>
    <description lang="en">This module allows Dynacase to connect to FOO</description>

### Dépendances entre modules

Les dépendances permettent d'exprimer qu'un module requiert d'autres modules
Dynacase avec eventuellement une contrainte sur la version de ceux-ci.

Le tag `<requires>` est composés d'éléments `<module>` qui ont les attributs suivants :

name
:   le nom du module Dynacase requis

version
:   la version que le module requis doit avoir

comp
:   opérateur de comparaison de version : `lt` (<), `le` (<=), `gt` (>), `ge` (>=), `eq` (==) ou `ne` (!=)

Le module peut aussi exprimer une contrainte sur la version de l'installeur lui
même à l'aide de l'élément `<installer>`.  Dans ce cas, les attributs sont :

version
:   la version de l'installeur que requiert le module

comp
:   opérateur de comparaison de version : `lt` (<), `le` (<=), `gt` (>), `ge` (>=), `eq` (==) ou `ne` (!=)

Exemple :

      [xml]
      <requires>
        <installer version="1.0" comp="ge" />
        <module name="dynacase-bar" version="2.0" comp="ge" />
        <module name="dynacase-baz" version="1.9" comp="gt" />
      <requires>

Dans cet exemple, le module requiert un installeur avec une version >= 1.0, le
module dynacase-bar en version >= 2.0 et le module dynacase-baz en version >1.9.

### Organisation des instructions d'importation des familles/documents

Les contrôles d’importation vérifient la validité des différentes structures et
des relations entre les documents.

L’ordre que nous préconisons est le suivant :

| Définition                                       | Description de l’opération                                                                                                                                                                                                                                                                                                  |
| -                                                | -                                                                                                                                                                                                                                                                                                                           |
| Définition de l’application                      | Instructions de définition de l’application. <br> Lors de l’installation, l'instruction se résume à `<process command="programs/record_application @APPNAME@"/>`. <br> Lors de l’upgrade, elle devient `<process command="programs/pre_migration @APPNAME@"/>` `<process command="programs/record_application @APPNAME@"/>` |
| Groupes et utilisateurs                          | Document qui importe les groupes et les utilisateurs utilisés dans cette application                                                                                                                                                                                                                                        |
| Droits                                           | Document associant les droits spécifiques à l’application (ACL) aux groupes et aux utilisateurs importés auparavant                                                                                                                                                                                                         |
| Famille(s) père(s)                               | Si la famille en cours possède une famille père qui n’est pas déjà existante, vous devez l’importer avec l’ensemble de ses dépendances                                                                                                                                                                                      |
| Structure de la famille en cours                 | Uniquement la structure de la famille en terme d’attributs/paramètres                                                                                                                                                                                                                                                       |
| Famille de workflow                              | Définition de la famille de workflow associée à la famille en cours.                                                                                                                                                                                                                                                        |
| Paramétrage du workflow                          | Documents de workflow et de paramétrage associés (profils dédiés, masques, timers…)                                                                                                                                                                                                                                         |
| Paramétrage de la famille                        | Documents associés à la famille (profil de document, profil de famille, contrôle dédié, etc...)                                                                                                                                                                                                                             |
| Propriétés de la famille                         | Ensemble des propriétés de la famille (icône, label, référence du profil de famille, référence du profil de document par défaut, référence du contrôle de vue, référence du cycle de vie associé…)                                                                                                                          |
| Documents de la famille en cours                 | Éventuels documents de la famille en cours d’importation                                                                                                                                                                                                                                                                    |
| Famille(s) suivante(s)                           | Autres familles de l’application, éventuellement héritant de la famille en cours                                                                                                                                                                                                                                            |
| Lancement des scripts d'upgrade de l'application | `<process command="programs/post_migration @APPNAME@" />`                                                                                                                                                                                                                                                                   |
| Regénération des catalogues de langues           | `<process command="programs/update_catalog" />`                                                                                                                                                                                                                                                                             |
| Instructions diverses                            | Toutes les instructions spacifiques à votre projet                                                                                                                                                                                                                                                                          |

Cela donne le XML suivant :

    [xml]
    <?xml version="1.0"?>
    
    <!-- The @SOMETHING@ variables are completed by the autoconf mechanism -->
    <module name="@PACKAGE@" version="@VERSION@" release="@RELEASE@">

       <!-- Label of the application displayed in dynacase control only -->
       <description>My application name</description>
       
       <!-- Module required with version for the installation/upgrade -->
       <requires>
           <module name="dynacase-platform" version="3.2.0" comp="ge"/>
       </requires>

       <!-- Install instruction -->
       <post-install>
           <process command="programs/record_application @APPNAME@"/>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/GROUPS.ods">
               <label lang="en">Importing GROUPS.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/RIGHT.ods">
               <label lang="en">Importing RIGHT.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/STRUCT_famille_A.ods">
               <label lang="en">Importing STRUCT_famille_A.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/PARAM_famille_A.ods">
               <label lang="en">Importing PARAM_famille_A.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/PROPERTY_famille_A.ods">
               <label lang="en">Importing PROPERTY_famille_A.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/STRUCT_famille_B.ods">
               <label lang="en">Importing STRUCT_famille_B.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/WFL_famille_B.ods">
               <label lang="en">Importing WFL_famille_B.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/PARAM_WFL_famille_B.ods">
               <label lang="en">Importing PARAM_WFL_famille_B.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/PARAM_famille_B.ods">
               <label lang="en">Importing PARAM_famille_B.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/PROPERTY_famille_B.ods">
               <label lang="en">Importing PROPERTY_famille_B.ods</label>
           </process>
           <process command="./wsh.php --api=importDocuments --file=./@APPNAME@/DOC_famille_B.ods">
               <label lang="en">Importing DOC_famille_B.ods</label>
           </process>
           <process command="programs/post_migration @APPNAME@"/>
           <process command="programs/update_catalog"/>
       </post-install>
    
       <post-upgrade>
           <process command="programs/pre_migration @APPNAME@"/>
           <process command="programs/record_application @APPNAME@"/>

           <!-- Same content as the content between record and post migration of the install part above -->

           [...]

           <process command="programs/post_migration @APPNAME@"/>
           <process command="programs/update_catalog"/>
       </post-upgrade>
    
    </module>

### Changelog

Le changelog permet d'indiquer les évolutions produites en rapport avec les
versions du module. Ces informations sont contenues dans une balise
`<changelog>` contenant des `<version>`.

Les éléments `<version>` ont les attributs suivants :

number
:   le numéro de version

date
:   la date de publication de la version

Les éléments `<version>` contiennent des éléments `<change>` qui décrivent
chaque changement effectué. Les éléments `<change>` ont les attributs suivants :

title
:   l'intitulé du changement

url
:   une url en rapport avec le changement (dans l'interface dynacase-control, les chaînes de forme issues/111/
    sont reconnues et donnent comme texte du lien affiché 'issues 111' ; sinon un texte par défaut est utilisé)

La valeur de l'élément `<change>` constitue une description.

Exemple :

    [xml]
    <changelog>
    <version number="1.1.1" date="2009-01-01">
      <change title="First change" url="http://dev.dynacase.org/issues/111">Comment for first change.</change>
      <change title="Second change">Comment for second change.</change>
      <change title="Third change"></change>
    </version>
    <version number="1.1.0" date="2008-12-15"/>
    </changelog>

### Paramètres d'installation/upgrade

Un module peut demander lors de son installation (ou upgrade) l'entrée de
certains paramètres d'installation ou d'upgrade.

Les paramètres d'installation/upgrade nécessaires au module sont spécifiés avec
un élément `<parameters>` contenant des éléments `<param>`.

La valeur du paramètre peut ensuite être lue par un programme lancé lors de
l'install/upgrade via le mécanisme de `<process />` décrit [ci-dessous](#module-wf-install-upgrade).

Note : ces paramètres de module n'ont pas de lien avec les paramètres
d'applications ou de familles de dynacase-platform. Par contre, un `<process/>`
spécifique peut être déclaré et utilisé pour enregistrer un paramètre de
module comme valeur d'un paramètre d'application et de famille en utilisant
par exemple le programme `program/set_param` décrit ci-dessous.

Les élements `<param>` ont les attributs suivants :

name
:   le nom du paramètre

label
:   le label textuel pour présenter le paramètre

type
:   `text` ou `enum`, permet de spécifier le type de donnée attendu

default
:   la valeur par défaut présenté à l'utilisateur lors de la saisie des paramètres.

needed
:   `yes` ou `no`, permet de spécifier si la saisie du paramètre est obligatoire ou optionnelle.

values
:   lorsque `type=“enum”`, l'attribut `values` permet de spécifier une list de choix finis à partir de laquelle l'utilisateur selectionnera une valeur

volatile
:   permet de spécifier si la valeur entrée doit être supprimée après l'installation ou upgrade.
    Cela permet d'indiquer que la valeur n'est pas mémorisée, et donc qu'elle sera demandé avec sa valeur par défaut à chaque upgrade du module par exemple  
    defaut `no`

oninstall
:   permet de spécifier la visibilité du paramètre lors d'une installation du module
    (`R` pour lecture seule,
    `W` pour lecture/écriture,
    `H` pour caché)  
    defaut `W`

onedit
:   permet de spécifier la visibilité du paramètre lors de l'édition
    des paramètres depuis la liste des modules installés  
    defaut `R`

onupgrade
:   permet de spécifier si le paramètre est re-demandé lors des upgrade du module  
    default `H`

Exemple :

      [xml]
      <parameters>
        <param name="foo_dir" label="Directory of FOO" type="text" default="/var/foo" needed="yes" />
        <param name="foo_color" label="Color of FOO" type="enum" values="red|green|blue" default="green" needed="no" />
      </parameters>

## Workflow d'install/upgrade (Pre/post install/upgrade, etc.) {#module-wf-install-upgrade}

Lors de l'installation, ou de l'upgrade, un ensemble d'actions peuvent être effectués
avant (pre) et après (post) l'opération suivant l'ordre suivant :

![Workflow install/upgrade d'un module](module-install-upgrade-workflow.png)

Chaque phase (pre-install, post-install, etc.) spécifie un ensemble de `check`
ou `process` qui sont exécutés et qui retournent un status d'échec ou de
réussite.

Une phase est validée lorsque tous ses sous-éléments `check` ou `process` ont
retourné un statut de réussite.

Si une phase n'est pas validée, alors les messages d'erreurs rencontrés
sont présenté à l'utilisateur , et celui-ci peut rejouer le check (ou le
process) après avoir eventuellement corrigé le problème, ou bien il peut
choisir d'ignorer les messages d'erreurs et poursuivre l'install/ugprade.

### Phase pre-install

Les éléments de pre-install s'exécutent avant l'installation des fichiers du
module sur le système de fichier.

Les actions possibles peuvent être des éléments `<check>`.

Chaque élément `<check>` peut fournir un élément `<help>` qui sera présenté à
l'utilisateur lorsque l'action échoue.

Exemple :

    [xml]
    <pre-install>
    <check type="phpfunction" function="pspell_new">
      <help>Il faut peut-être installer php5-pspell avec aspell et aspell-fr</help>
    </check>
    <check type="syscommand" command="convert" />
    </pre-install>

Les actions de pre-install serviront généralement à vérifier la présence de
certains éléments et à bloquer l'installation si ces éléments ne sont pas
présents/corrects.

### Phase post-install

Les éléments de post-install s'exécutent après l'installation des fichiers du
module sur le système de fichier.

Les actions possibles peuvent être des éléments `<process>`.

Chaque élément `<process>` peut fournir un élément `<label>` qui sera présenté
à l'utilisateur lorsque l'action sera exécutée.

Exemple :

    [xml]
    <post-install>
    <process command="programs/record_application FOO">
      <label lang="en">Record FOO application in database</label>
    </process>
    <process command="programs/update_catalog">
      <label lang="en">Generate localization catalog</label>
    </process>
    </post-install>

Les actions de post-install serviront généralement à configurer le module qui
vient d'être installé. Une erreur dans la phase de post-install laissera les
fichiers installés en place, mais le paquet sera marqué en erreur de
post-install dans l'interface.

### Phase pre-upgrade

Les éléments de pre-upgrade s'exécutent avant l'installation des nouveaux
fichiers du module sur le système de fichiers.

Les actions possibles peuvent être des éléments `<check>`.

Chaque élément `<check>` peut fournir un élément `<help>` qui sera présenté à
l'utilisateur lorsque l'action échoue.

Exemple :

    [xml]
    <pre-upgrade>
    <check type="phpfunction" function="pspell_new">
      <help>Il faut peut-être installer php5-pspell avec apspell et aspell-fr</help>
    </check>
    <check type="syscommand" command="convert" />
    </pre-upgrade>

Les actions de pre-upgrade serviront généralement à vérifier la présence de
certains éléments et bloquer l'upgrade si ces éléments ne sont pas
présents/corrects.

### Phase post-upgrade

Les éléments de post-upgrade s'exécutent après l'installation des nouveaux
fichiers du module sur le système de fichier.

Les actions possible peuvent être des éléments `<process>`.

Chaque élément `<process>` peut fournir un élément `<label>` qui sera présenté
à l'utilisateur lorsque l'action sera exécuté.

Exemple :

    [xml]
    <post-upgrade>
    <process command="programs/pre_migration FOO">
      <label lang="en">Pre-migration scripts</label>
    </process>
    <process command="programs/record_application FOO">
      <label lang="en">Update application record in database</label>
    </process>
    <process command="programs/post_migration FOO">
      <label lang="en">Post-migration scripts</label>
    </process>
    <process command="programs/update_catalog">
      <label lang="en">Re-generate localization catalog</label>
    </process>
    </post-install>

Les actions de post-upgrade serviront généralement à configurer le module qui
vient d'être installé, lancer les scripts de migration, etc. Une erreur dans la
phase de post-upgrade laissera les fichiers installés en place, mais le paquet
sera marqué en erreur de post-upgrade dans l'interface.

### Phase reconfigure

Les éléments de reconfigure s'exécutent après la restauration d'un contexte
depuis une archive.

Les actions possible sont les mêmes que pour les phases de `post-install` ou `post-upgrade`.

## Les actions de phase

### Check

Les éléments `check` permettent d'executer des actions pour vérifier la
présence de certains éléments.

phpfunction
:   Le check de type `phpfunction` permet de vérifier la présence d'une fonction PHP.  
    Le nom de la fonction testé est spécifié avec l'attribut `function`.
Exemple :

    <check type="phpfunction" function="pg_connect" />

syscommand
:   Le check de type `syscommand` permet de vérifier la présence d'une commande disponible sur le système.  
    Le nom de la command testé est spécifié avec l'attribut `command`

Exemple :

      <check type="syscommand" command="convert" />

phpclass
:   Le check de type `phpclass` permet de vérifier la présence d'une classe objet PHP.  
    Le nom de la classe PHP est fournis avec les attributs suivants :  
    *include* : le nom du fichier pour inclure la définition de la classe  
    *class* : le nom de la classe

Exemple :

      <check type="phpclass" include="Net/SMTP.php" class="Net_SMTP" />

apachemodule
:   Le check de type `apachemodule` permet de vérifier qu'un module Apache particulier est activé et chargé par celui-ci.  
    Le nom du module est spécifié par l'attribut `module`.

Exemple :

      <check type="apachemodule" module="mod_expires" />

### Process

Les éléments process servent à exécuter des commandes/programmes permettant
d'effectuer les opérations nécessaires au fonctionnement du module suite à son
installation.

#### Commande *programs/app_post*

Prototype :

* `programs/app_post <APPNAME> I|U`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Conditions d'utilisation :

* Dans la phase `post-install`, le programme doit être exécuté
  avec le programme `record_application`
* Dans la phase `post-upgrade`, le programme doit être exécuté
  après `pre_migration` et avant le programme `record_application`

Le programme `app_post` recherche et exécute, s'il est présent, le script
`./<APPNAME>/<APPNAME>_post` qui peut contenir des instructions pour un
traitement d'initialisation ou d'upgrade particulière.

Le module peut donc fournir son propre script nommé
`./<APPNAME>/<APPNAME>_post` qui sera exécuté par l'appel à
`programs/app_post`.

Les arguments sont :

* Le nom de l'application (le script exécuté sera alors &lt;APPNAME&gt;_post)
* `I` | `U` : Le nom de la phase d'initialisation a exécuter (`I` pour install, `U` pour upgrade)

Exemple :

      [xml]
      <post-install>
        <process command="programs/app_post WEBDESK I" />
        [...]
      </post-install>

      <post-upgrade>
        [...]
        <process command="programs/app_post WEBDESK U" />
        [...]
      </post-upgrade>

Le script `<APPNAME>_post` est alors exécuté par `app_post` avec un seul
argument qui est l'argument `I` ou `U` suivant qu'on est dans une installation
ou un upgrade.

    "programs/app_post FOO I" --(execute)--> "FOO/FOO_post I"

#### Commande *programs/record_application*

Prototype :

* `programs/record_application <APPNAME>`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Conditions d'utilisation :

* Le programme doit être exécuté après le programme `app_post`

Le programme `record_application` est utilisé pour enregistrer, ou mettre à
jour, la définition de l'application en base de données.

La ligne de commande est spécifié par l'attribut `command`.

`record_application` prend en argument le nom de l'application à enregistrer.

Exemple :

      <process command="programs/record_application FOO" />

#### Commande *programs/update_catalog*

Prototype :

* `programs/update_catalog`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Conditions d'utilisation :

* Le programme doit être exécuté à la fin de la phase

Le programme `update_catalog` est utilisé pour ré-générer le catalogue des
messages de localisation.

Exemple :

      [xml]
      <process command="programs/update_catalog" />

#### Commande *programs/pre_migration*

Prototype :

* `programs/pre_migration`

Utilisable dans les phases :

* `post-upgrade`

Conditions d'utilisation :

* Le programme doit être exécuté au début de la phase, avant le programme
  `app_post`

Le programme `pre_migration` est utilisé pour exécuter les scripts de
pre-migration d'un module lors de sa mise-à-jour.

Exemple :

      <process command="programs/pre_migration" />

#### Commande *programs/post_migration*

Prototype :

* `programs/post_migration`

Utilisable dans les phases :

* `post-upgrade`

Conditions d'utilisation :

* Le programme doit être exécuté après le programme `record_application`, et
  avant le `update_catalog`

Le programme `post_migration` est utilisé pour exécuter les scripts de
post-migration d'un module lors de sa mise-à-jour.

Exemple :

      [xml]
      <process command="programs/post_migration" />

#### Commande *programs/set_param*

Prototype :

* `programs/set_param <DYNACASE_PLATFORM_PARAM_NAME> <DYNACASE_CONTROL_MODULE_PARAM_NAME>`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Le programme `programs/set_param` permet de positionner la valeur d'un
paramètre applicatif de dynacase-platform avec la valeur d'un paramètre de
module.

Exemple :

    [xml]
    <parameters>
      <param name="foo_client_name" label="Client name" type="text" default="ACME" onupgrade="W"/>
    </parameters>

    <post-install>
      [...]
      <process command="programs/set_param CORE_CLIENT foo_client_name" />
      [...]
    </post-install>

#### Commande *./wsh.php*

Prototype :

* `./wsh.php`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Conditions d'utilisation :

* Le programme peut être utilisé à n'importe quel moment en fonction des
  besoins

Le programme `./wsh.php` est utilisé pour exécuter des méthodes sur des classes
documentaires et exécuter des API Dynacase.

Exemple :

      <process command="./wsh.php --api=refreshDocuments --method=postModify --famid=FOO" />

#### Programmes personnalisés

Vous avez la possibilité d'écrire vos propres programmes de post-install,
post-upgrade, etc. afin d'effectuer des opérations spécifiques à votre module.

Ces programmes seront généralement développés soit en shell Bash soit en PHP.
Ils seront disponible après la phase de décompression de votre paquet, dans le
répertoire que vous aurez spécifié à l'empaquetage.

Le programme est exécuté dans le répertoire racine de l'installeur
DYNACASE-CONTROL, et les variables d'environnement suivantes sont accessible
depuis le script :

* `$WIFF_ROOT` : Le chemin du répertoire ou est installé DYNACASE-CONTROL
  (c'est donc aussi le répertoire courant (`$CWD`) dans lequel sera exécuté
  votre programmes)
* `$WIFF_CONTEXT_ROOT` : Le chemin du répertoire du contexte sur lequel est
  effectué l'opération.
* `$WIFF_CONTEXT_NAME` : Le nom du contexte sur lequel est effectué
  l'opération.

##### Ecrire un programme personnalisé en shell Bash

Exemple :

    [bash]
    #!/bin/bash

    set -e

    # -- Récupérer la valeur du paramètre `foo_dir' spécifié par l'utilisateur
    PARAM_FOO_DIR=`"$WIFF_ROOT"/wiff --getValue=foo_dir`

    # -- Créer le répertoire s'il n'existe pas
    if [ ! -d "$PARAM_FOO_DIR" ];
      mkdir "$PARAM_FOO_DIR"
    fi

    # -- Ajouter le nom de ce répertoire dans le fichier
    # -- `foo_dir.list' dans le sous-répertoire de mon module `FOO'
    echo "$PARAM_FOO_DIR" >> "$WIFF_CONTEXT_ROOT"/FOO/foo_dir.list

##### Ecrire un programme personnalisé en PHP {#module-programme-perso}

Note : Le programme PHP a aussi accès aux variables d'environnement,
comme le script Bash, mais le chemin d'include doit être construit
en fonction de vos besoins.

Exemple :

    [php]
    #!/usr/bin/env php
    <?php

    $WIFF_ROOT=getenv('WIFF_ROOT');
    if( $WIFF_ROOT === false ) {
      print "WIFF_ROOT environment variable is not set!";
      exit( 1 );
    }

    $WIFF_CONTEXT_ROOT=getenv('WIFF_CONTEXT_ROOT');
    if( $WIFF_CONTEXT_ROOT === false ) {
      print "WIFF_CONTEXT_ROOT environment variable is not set!";
      exit( 1 );
    }

    # -- Si je dois accéder aux fichier d'include de Dynacase
    # -- j'ajoute les répertoires d'include de Dynacase
    # -- dans mon include_path PHP
    set_include_path(join(PATH_SEPARATOR, array(
      get_include_path(),
      "$WIFF_ROOT/include",
      "$WIFF_CONTEXT_ROOT"
    )));

    # -- A présent, je peux inclure les librairies de l'installeur
    require("lib/Lib.Cli.php");
    # -- ... et les librairies Dynacase
    require("WHAT/Lib.Common.php");

    $param_foo_dir = wiff_getParamValue('foo_dir');
    if( ! is_dir($param_foo_dir) ) {
      $ret = mkdir($param_foo_dir);
      if( $ret === false ) {
        print sprintf("Error: could not create directory '%s'!", $param_foo_dir);
        exit( 1 );
      }
    }

### Download

Les éléments download servent à télécharger un fichier et exécuter un
traitement sur le fichier téléchargé. Il est utilisé par exemple pour
télécharger du code tier livré sous forme d'archive Zip ou Tar.gz, et de
l'installer dans le contexte.

Exemple :

    [xml]
    <parameters>
      <param name="foo_url" label="Foo download URL" type="text" default="http://www.example.net/foo/foo-1.0.0.zip" volatile="yes" />
    </parameters>

    <download href="@foo_url" action="programs/foo_install">
      <label lang="en">Downloading and installing Foo</label>
    </download

L'élément download est généralement couplé à un paramètre qui permet de
demander et modifier l'URL de la ressource à télécharger.

Les propriétés suivantes sont utilisables sur l'élément download :

href
:   contient l'URL de la ressource à télécharger, ou bien la valeur
    d'un paramètre demandé précédemment avec la notation `@nom_du_parametre`.

action
:   contient le chemin d'un script qui sera exécuté avec le fichier
    téléchargé comme argument : `programs/foo_install /tmp/foo_url_xxx`.
    L'environnement d'exécution est identique à celui décrit dans la section
    [Programmes personnalisé](#module-programme-perso) ci-dessus.

## Variables dans les actions de phase

Certaines propriétés d'actions peuvent contenir des variables.
Ces variables permettent alors de référencer et d'utiliser la valeur d'un paramètre de module.

La liste des actions et des propriétés supportant l'évaluation de variables est décrite ci-dessous.

### Notation

Les variables peuvent être exprimées avec les notations suivantes :

* `@PARAM_NAME`
* `@{PARAM_NAME}`

Pour entrer un caractère `@` literal il faut doubler le caractère :

* `@@` → `@`

Les noms des variables doivent être de la forme `[a-zA-Z_][a-zA-Z0-9_]*`.

### Actions supportant les variables

#### Action *download*

Dans une action `download`, les propriétés suivantes supportent l'évaluation des variables :

* `href`

Exemple :

    <download href="... @PARAM_NAME ..." />

#### Action *process*

Dans une action `process`, les propriétés suivantes supportent l'évaluation des variables :

* `command`

Exemple :

    <process command="... @PARAM_NAME ..." />

#### Action *check* de type *exec*

Dans une action `check` de type `exec`, les propriétés suivantes supportent l'évaluation des variables :

* `cmd`

Exemple :

    <check type="exec" cmd="... @PARAM_NAME ..." />

