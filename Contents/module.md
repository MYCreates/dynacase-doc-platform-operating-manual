# Format des modules webinst {#module}

Dans ce paragraphe nous allons détailler les éléments constitutifs d'un module
webinst et mettre en œuvre ces éléments pour construire un module d'exemple
que nous nommerons *dynacase-foo*.

Pour bien suivre cette présentation, il est souhaitable d'avoir bien en tête
les notions d'Applications et d'Actions de Dynacase et le fonctionnement
général de ceux-ci.

## Format d'une archive `webinst`

Une archive `webinst` de module est une archive [TAR][gnu_tar] compressée par
[GZIP][gzip] avec pour extension `.webinst`.

L'archive contient les éléments suivants :

`content.tar.gz` (requis)
:   Une archive ([TAR][gnu_tar] compressé [GZIP][gzip]) contenant les fichiers
    à  déployer dans le contexte.
    
    Cette archive est décompressée dans la racine du contexte sous l'identité
    de l'utilisateur configuré lors de l'installation de dynacase-core.
    
    Les chemins des fichiers contenus dans l'archive sont alors relatifs à la
    racine du contexte.

`info.xml` (requis)
:   Un fichier au format XML décrivant le module et les opérations à exécuter
    pour son installation, mise-à-jour, etc.
    
    Voir [Fichier `info.xml`][info_xml] pour la description de ce fichier.

`LICENSE` (optionnel)
:   Un fichier contenant le texte de la licence du module au format texte brut
    (`text/plain`).
    
    Si ce fichier est présent et que la propriété `license` du module est
    valuée dans le fichier fichier `info.xml`, alors le contenu de ce fichier
    est présenté à l'utilisateur pour que ce dernier valide la licence du
    module.

## Fichier `info.xml` {#manex-ref:22c8b9e1-1a44-42e9-bc6e-68f3373beac6}

Le fichier `info.xml` permet de décrire le module Dynacase en fournissant en
particulier :

* le nom du module,
* la version du module,
* une description,
* une liste de dépendances avec d'autres modules Dynacase,
* un ensemble d'action de pre-install, post-install,
* un ensemble de paramètres,

### Exemple de fichier de info.xml {#manex-ref:7ed1e3fa-7d6b-4563-b78c-1f7f34cc233b}

    [xml]
    <?xml version="1.0"?>
    <module name="dynacase-foo" version="1.2.3" release="1" basecomponent="no" changelog="https://modules.example.net/changelog/dynacase-foo/1.2.3">
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

## Description d'un module {#manex-ref:da659161-6bf6-4dfa-80d5-be43ce65ab25}

### Préambule Module {#manex-ref:d168e9ea-78dc-4e23-b2a6-814bee0e4e2e}

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

license
:   license du module : nom/label de la license du module (ex. `http://www.fsf.org/licensing/licenses/agpl-3.0.html GNU Affero General Public License`).
    
    Si un fichier `LICENSE` (contenant le texte de la licence au format
    text/plain) est présent à la racine de l'archive `.webinst`, alors une
    fenêtre est ouverte avec le contenu de ce fichier `LICENSE` lorsque le
    module est installé pour la première fois afin que l'utilisateur accepte
    la licence. *optionnel*

vendor
:   fournisseur du module (ex. `ACME Corp.`)  
    *optionnel*

changelog
:   URL d'une page contenant le changelog du module.
    *optionnel*

Exemple :

    [xml]
    <?xml version="1.0"?>
    <module
        name="dynacase-foo"
        version="1.2.3"
        release="rc1"
        author="John Doe &lt;john.doe@example.net&gt;"
        license="GPLV2"
        vendor="ACME Corp."
        changelog="https://modules.example.net/changelog/dynacase-foo/1.2.3"
    >
    
    […]
    
    </module>

### Description {#manex-ref:aa5e19bc-1809-42d2-a165-98736747b1da}

Le module peut fournir une description textuelle pour expliciter le rôle du module.
On pourra fournir des descriptions localisées en utilisant l'attribut `lang`.

Exemple :

    [xml]
    <description lang="fr">Ce module permet à Dynacase de se connecter à FOO</description>
    <description lang="en">This module allows Dynacase to connect to FOO</description>

### Dépendances entre modules {#manex-ref:bf541424-6320-4eb7-8e50-16ec590e251d}

Les dépendances permettent d'exprimer qu'un module requiert d'autres modules
Dynacase avec éventuellement une contrainte sur la version de ceux-ci.

Le tag `<requires>` est composés d'éléments `<module>` qui ont les attributs suivants :

name
:   le nom du module Dynacase requis

version
:   la version que le module requis doit avoir

comp
:   opérateur de comparaison de version : `lt` (<), `le` (<=), `gt` (>), `ge` (>=), `eq` (==) ou `ne` (!=)

Le module peut aussi exprimer une contrainte sur la version de l'installeur, Dynacase Control, lui
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

### Organisation des instructions d'importation des familles/documents {#manex-ref:15717d28-034c-466c-91be-0dee27b636d5}

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

### Paramètres d'installation/upgrade {#manex-ref:3b643797-d2e5-4906-9c81-a6e59bd93bbd}

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

## Workflow d'install/upgrade/archive/restore (Pre/post install/upgrade, etc.) {#module-wf-install-upgrade}

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
process) après avoir éventuellement corrigé le problème, ou bien il peut
choisir d'ignorer les messages d'erreurs et poursuivre l'install/ugprade.

### Phase pre-install {#manex-ref:a57ff321-d00f-4f55-ba88-6633a686d856}

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

### Phase post-install {#manex-ref:ced835d9-ce84-420d-850a-dc655cd203fa}

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

### Phase pre-upgrade {#manex-ref:894dd826-0669-4ce1-aef5-866b4f9b612f}

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

### Phase post-upgrade {#manex-ref:ccd2c732-1932-4f8c-bf1c-7311dc48f1de}

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
    </post-upgrade>

Les actions de post-upgrade serviront généralement à configurer le module qui
vient d'être installé, lancer les scripts de migration, etc. Une erreur dans la
phase de post-upgrade laissera les fichiers installés en place, mais le paquet
sera marqué en erreur de post-upgrade dans l'interface.

### Phase reconfigure {#manex-ref:d3679ce1-5475-4de2-a816-b399d881e5c0}

Les éléments de reconfigure s'exécutent après la restauration d'un contexte
depuis une archive.

Les actions possible sont les mêmes que pour les phases de `post-install` ou `post-upgrade`.

### Phase pre-archive {#manex-ref:d31adf2b-c2c6-4536-8473-8cbd415f9228}

Les éléments de pre-archive s'exécutent avant l'archivage d'un contexte.

Les actions possibles sont les mêmes que pour les phases de `post-install` ou
`post-upgrade`.

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure d'archivage.

### Phase post-archive {#manex-ref:455076b2-2265-43cb-9258-4b18006025e7}

Les éléments de post-archive s'exécutent après l'archivage du contexte.

Les actions possible sont les mêmes que pour les phases de `post-install` ou
`post-upgrade`.

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure d'archivage.

### Phase pre-restore {#manex-ref:4f34f078-29f8-4097-8bcd-2a429d20c1b6}

Les éléments de pre-restore s'exécutent avant la restauration d'un contexte à
partir d'une archive.

Les actions possible sont les mêmes que pour les phases de `post-install` ou
`post-upgrade`.

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure de restauration.

### Phase post-restore {#manex-ref:0b92006c-f874-4cbb-977c-d8c67c0935a8}

Les éléments de post-restore s'exécutent après la restauration d'un contexte à
partir d'un archive et après l'exécution de la phase de
[reconfigure][reconfigure].

Les actions possible sont les mêmes que pour les phases de `post-install` ou
`post-upgrade`.

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure de restauration.

## Les actions de phase {#manex-ref:66ae8fce-e24c-4e1c-b825-fe0de5d12aee}

### Check {#manex-ref:dd6c4985-b5eb-4a0e-a4fb-d3ec067cfa2d}

Les éléments `check` permettent d'executer des actions pour vérifier la
présence de certains éléments.

phpfunction
:   Le check de type `phpfunction` permet de vérifier la présence d'une fonction PHP.  
    Le nom de la fonction testé est spécifié avec l'attribut `function`.
    
    Exemple :

    [xml]
    <check type="phpfunction" function="pg_connect" />

syscommand
:   Le check de type `syscommand` permet de vérifier la présence d'une commande disponible sur le système.  
    Le nom de la command testé est spécifié avec l'attribut `command`
    
    Exemple :

    [xml]
    <check type="syscommand" command="convert" />

phpclass
:   Le check de type `phpclass` permet de vérifier la présence d'une classe objet PHP.  
    Le nom de la classe PHP est fournis avec les attributs suivants :  
    *include* : le nom du fichier pour inclure la définition de la classe  
    *class* : le nom de la classe
    
    Exemple :

    [xml]
    <check type="phpclass" include="Net/SMTP.php" class="Net_SMTP" />

apachemodule
:   Le check de type `apachemodule` permet de vérifier qu'un module Apache particulier est activé et chargé par celui-ci.  
    Le nom du module est spécifié par l'attribut `module`.
    
    Exemple :

    [xml]
    <check type="apachemodule" module="mod_expires" />

### Process {#manex-ref:796c1c2a-95b8-4bd6-86dd-4ecada2ea22d}

Les éléments process servent à exécuter des commandes/programmes permettant
d'effectuer les opérations nécessaires au fonctionnement du module suite à son
installation.

Si le programme référencé par la propriété `command` commence par un `/`
(slash), alors le chemin du programme est considéré comme étant absolu. Dans
le cas contraire, le chemin du programme est préfixé avec le chemin de la
racine du contexte.

Exemples :

    [xml]
    <process command="programs/foo arg1 arg2 arg3"

La commande exécutée sera `${WIFF_CONTEXT_ROOT}/programs/foo` avec les
arguments `arg1`, `arg2` et `arg3`.

    [xml]
    <process command="/usr/local/bin/foo arg1 arg2 arg3"

La commande exécutée sera `/usr/local/bin/foo` avec les arguments `arg1`,
`arg2` et `arg3`.

Une ensemble de programmes/commandes sont livrés par défaut et leur
fonctionnement est décrit ci-dessous.

#### Commande *programs/app_post* {#manex-ref:cc8bc194-900b-427a-9f5c-f4b629e81daa}

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

#### Commande *programs/record_application* {#manex-ref:c695dfd3-dcf6-4d52-a72e-ec95cb15450e}

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

    [xml]
    <process command="programs/record_application FOO" />

#### Commande *programs/update_catalog* {#manex-ref:a3b5b826-2476-4602-8772-4c8e264de1ed}

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

#### Commande *programs/pre_migration* {#manex-ref:1dbb94a3-763f-46f7-bb2d-d7029dcf8592}

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

    [xml]
    <process command="programs/pre_migration" />

#### Commande *programs/post_migration* {#manex-ref:6a525876-dac7-40c3-8f87-01a9bec64837}

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

#### Commande *programs/set_param* {#manex-ref:39167ac3-e1e7-4d07-a784-ef9f365a6457}

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

#### Commande *wsh.php* {#manex-ref:b7ad8be9-55d9-48e3-80e4-9245d6f4f345}

Prototype :

* `wsh.php`

Utilisable dans les phases :

* `post-install`
* `post-upgrade`

Conditions d'utilisation :

* Le programme peut être utilisé à n'importe quel moment en fonction des
  besoins

Le programme `wsh.php` est utilisé pour exécuter des méthodes sur des classes
documentaires et exécuter des API Dynacase.

Exemple :

    [xml]
    <process command="wsh.php --api=refreshDocuments --method=postModify --famid=FOO" />

#### Programmes personnalisés {#manex-ref:afc3d392-bd87-418a-af0e-ceb8924e74a2}

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

##### Ecrire un programme personnalisé en shell Bash {#manex-ref:91aa0dd8-af31-4ade-892c-1ac64c7545e6}

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

### Download {#manex-ref:79df376c-b15c-4585-9066-5da67dc798dd}

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

## Variables dans les actions de phase {#manex-ref:3a0d54fd-8005-4f1b-98e9-89df67930c1f}

Certaines propriétés d'actions peuvent contenir des variables.
Ces variables permettent alors de référencer et d'utiliser la valeur d'un paramètre de module.

La liste des actions et des propriétés supportant l'évaluation de variables est décrite ci-dessous.

### Notation {#manex-ref:3682b0bb-0ab7-4ea8-90c4-4b6af58f150c}

Les variables peuvent être exprimées avec les notations suivantes :

* `@PARAM_NAME`
* `@{PARAM_NAME}`

Pour entrer un caractère `@` literal il faut doubler le caractère :

* `@@` → `@`

Les noms des variables doivent être de la forme `[a-zA-Z_][a-zA-Z0-9_]*`.

### Actions supportant les variables {#manex-ref:81c7354d-2585-4cbf-a770-c5e3b70a9fca}

#### Action *download* {#manex-ref:b4476cf1-5bcf-44b4-8e4f-a8e200f54abf}

Dans une action `download`, les propriétés suivantes supportent l'évaluation des variables :

* `href`

Exemple :

    [xml]
    <download href="... @PARAM_NAME ..." />

#### Action *process* {#manex-ref:132a458a-2fd4-4a9b-847d-e44a0e48a870}

Dans une action `process`, les propriétés suivantes supportent l'évaluation des variables :

* `command`

Exemple :

    [xml]
    <process command="... @PARAM_NAME ..." />

#### Action *check* de type *exec* {#manex-ref:461dd848-f425-4b8d-80ce-eaeb63cba2a5}

Dans une action `check` de type `exec`, les propriétés suivantes supportent l'évaluation des variables :

* `cmd`

Exemple :

    [xml]
    <check type="exec" cmd="... @PARAM_NAME ..." />

<!-- links -->
[gnu_tar]: https://www.gnu.org/software/tar/
[gzip]: http://www.gzip.org/
[info_xml]: #manex-ref:22c8b9e1-1a44-42e9-bc6e-68f3373beac6
[reconfigure]: #manex-ref:d3679ce1-5475-4de2-a816-b399d881e5c0