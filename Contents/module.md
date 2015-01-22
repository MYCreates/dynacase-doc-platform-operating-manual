# Format des modules webinst {#manex-ref:f28ae532-05cf-4a2d-a959-fbf258f1a778}

<span class="flag inline release from">control 1.5</span>

Dans ce paragraphe nous allons détailler les éléments constitutifs d'un module
webinst et mettre en œuvre ces éléments pour construire un module d'exemple
que nous nommerons *dynacase-foo*.

Pour bien suivre cette présentation, il est souhaitable d'avoir bien en tête
les notions d'Applications et d'Actions de Dynacase et le fonctionnement
général de celles-ci.

Pour un aperçu rapide des changements apportés par rapport à l'ancien format,
voir la section "[Changements par rapport à l'ancien format][changes]" ci-
dessous.

## Format d'une archive `webinst` {#manex-ref:20c15b85-abe1-4ed6-9f38-d6df7489a877}

Une archive `webinst` de module est une archive [TAR][gnu_tar] compressée par
[GZIP][gzip] avec pour extension `.webinst`.

L'archive contient les éléments suivants :

`content.tar.gz` (requis)
:   Une archive ([TAR][gnu_tar] compressé [GZIP][gzip]) contenant les fichiers
    à déployer dans le contexte.
    
    Les chemins des fichiers contenus dans l'archive doivent être relatifs à
    la racine du contexte et êtres conformes à l'arborescence attendue sur le
    serveur.
    
    Cette archive est décompressée dans la racine du contexte sous l'identité
    de l'utilisateur configuré lors de l'installation de dynacase-core.

`info.xml` (requis)
:   Un fichier au format XML conforme au schéma XML
    [`webinst-module-1.0.xsd`][xsd] décrivant le module et les opérations à
    exécuter pour son installation, mise-à-jour, etc.
    
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
* un ensemble d'actions de pre-install, post-install,
* un ensemble de paramètres,

### Exemple de fichier de info.xml {#manex-ref:7ed1e3fa-7d6b-4563-b78c-1f7f34cc233b}

    [xml]
    <?xml version="1.0"?>
    <module xmlns="urn:dynacase:webinst:module/1.0" name="dynacase-foo" version="1.2.3" release="1" basecomponent="N" changelog="https://modules.example.net/changelog/dynacase-foo/1.2.3">
      <description>dynacase foo</description>
    
      <requires>
        <installer version="1.4.0" comp="ge" />
        <module name="dynacase-bar" />
        <module name="dynacase-baz" version="1.0.0" comp="ge" />
      </requires>
    
      <parameters>
        <param name="foo_dir" label="Directory of FOO" type="text" default="/var/foo" needed="Y" />
        <param name="foo_color" label="Color of FOO" type="enum" values="red|green|blue" default="green" needed="N" />
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
        <process command="programs/record_application FOO I" />
        <process command="programs/app_post FOO U" />
        <process command="programs/update_catalog" />
      </post-install>
    
      <post-upgrade>
        <process command="programs/pre_migration FOO" />
        <process command="programs/app_post FOO U" />
        <process command="programs/record_application FOO U" />
        <process command="programs/post_migration FOO" />
        <process command="programs/update_catalog" />
      </post-upgrade>
    
      <reconfigure>
        <process command="FOO/reconfigure_foo" />
      </reconfigure>
    
    </module>

## Description d'un module {#manex-ref:da659161-6bf6-4dfa-80d5-be43ce65ab25}

La définition d'un module doit être conforme à la description XML
[`urn:dynacase:webinst:module/1.0`][xsd].

La définition au format XML Schema Definition (XSD) de ce format est consultable
et téléchargeable sur le dépôt GitHub suivant:

* https://github.com/Anakeen/dynacase-xml-schemas

Le fichier XSD de définition des modules est [`webinst-module-1.0.xsd`][xsd].

### Préambule Module {#manex-ref:d168e9ea-78dc-4e23-b2a6-814bee0e4e2e}

La racine du document `info.xml` est un tag `<module>` avec les attributs suivants :

xmlns
:   Le namespace XML `urn:dynacase:webinnst:module/1.0` utilisé par le
    module.

name
:   le nom du module

version
:   la version du module (sous la forme `N.N.N`)
    
    La comparaison des versions entre deux modules est faite à l'aide de la
    fonction PHP [`version_compare()`][php_version_compare].  La comparaison
    est faite en deux étapes : d'abord comparaison des valeurs `version`, puis,
    si les versions sont identiques, comparaison des valeurs `release`.

release
:   le numéro de release de la version

basecomponent (*optionnel*)
:   `Y` ou `N`, permet de spécifier si le module est un module de base.
    Dans ce cas, son installation sera obligatoire.
    
    Valeur par défaut : `N`

license (*optionnel*)
:   license du module : nom/label de la licence du module (ex. `http://www.fsf.org/licensing/licenses/agpl-3.0.html GNU Affero General Public License`).
    
    Si un fichier `LICENSE` (contenant le texte de la licence au format
    text/plain) est présent à la racine de l'archive `.webinst`, alors une
    fenêtre est ouverte avec le contenu de ce fichier `LICENSE` lorsque le
    module est installé pour la première fois afin que l'utilisateur accepte
    la licence.

vendor (*optionnel*)
:   fournisseur du module (ex. `ACME Corp.`).

changelog (*optionnel*)
:   URL d'une page contenant le changelog du module.

Exemple :

    [xml]
    <?xml version="1.0"?>
    <module
        xmlns="urn:dynacase:webinst:module/1.0"
        name="dynacase-foo"
        version="1.2.3"
        release="rc1"
        license="GPLV2"
        vendor="ACME Corp."
        changelog="https://modules.example.net/changelog/dynacase-foo/1.2.3"
    >
    
    […]
    
    </module>

### Description {#manex-ref:aa5e19bc-1809-42d2-a165-98736747b1da}

Le module peut fournir une description textuelle pour expliciter le rôle du
module.

Exemple :

    [xml]
    <description>This module allows Dynacase to connect to FOO</description>

### Dépendances {#manex-ref:bf541424-6320-4eb7-8e50-16ec590e251d}

Les dépendances permettent d'exprimer qu'un module requiert d'autres modules
Dynacase avec éventuellement une contrainte sur leur version, ou une version
spécifique de l'installeur.

Les dépendances sont exprimés à l'aide du tag `<requires>`.

#### Dépendances avec des modules {#manex-ref:d811d3f2-b553-43e3-b3c6-510d6e2a3523}

La dépendance avec un module est exprimée avec un élément `<module>` qui
comporte les attributs suivants :

name
:   le nom du module Dynacase requis

version
:   la version attendue pour le module requis

comp
:   opérateur de comparaison de version : `lt` (<), `le` (<=), `gt` (>), `ge` (>=), `eq` (==) ou `ne` (!=)

Exemple :

    [xml]
    <requires>
      <module name="dynacase-bar" version="2.0" comp="ge" />
      <module name="dynacase-baz" version="1.9" comp="gt" />
    <requires>

Dans cet exemple, le module requiert le module dynacase-bar en version >= 2.0
et le module dynacase-baz en version >1.9.

#### Dépendance avec l'installeur {#manex-ref:1dcb62fb-e40a-4cf0-9992-659773707a49}

Le module peut aussi exprimer une dépendance sur la version de l'installeur
Dynacase Control.

Dans ce cas, le tag `<requires>` peut contenir un élément `<installer>` avec
les attributs suivants :

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

### Paramètres d'installation/upgrade {#manex-ref:3b643797-d2e5-4906-9c81-a6e59bd93bbd}

Un module peut demander lors de son installation (ou upgrade) l'entrée de
certains paramètres d'installation ou d'upgrade.

Les paramètres d'installation/upgrade nécessaires au module sont spécifiés avec
un élément `<parameters>` contenant des éléments `<param>`.

La valeur du paramètre peut ensuite être lue par un programme lancé lors de
l'install/upgrade via le mécanisme de [Process](#module-wf-install-upgrade)
décrit ci-dessous.

Note : ces paramètres de module n'ont pas de lien avec les paramètres
d'applications ou de familles de dynacase-platform. Par contre, un `<process/>`
spécifique peut être déclaré et utilisé pour enregistrer un paramètre de
module comme valeur d'un paramètre d'application et de famille en utilisant
par exemple le programme `program/set_param`.

<p style="background-color: yellow">
[TODO] Faire le lien avec program/set_param.
</p>

Les éléments `<param>` ont les attributs suivants :

name
:   Le nom du paramètre.

label
:   Le label textuel pour présenter le paramètre.

type
:   `text` ou `enum`, permet de spécifier le type de donné attendu.

default (*optionnel*)
:   La valeur par défaut présentée à l'utilisateur lors de la saisie des
    paramètres.
    
    Valeur par défaut : `""` (chaîne vide).

needed (*optionnel*)
:   `Y` (pour yes) ou `N` (pour no), permet de spécifier si la saisie du
    paramètre est obligatoire ou optionnelle.
    
    Valeur par défaut : `N`.

values
:   Lorsque `type=“enum”`, l'attribut `values` permet de spécifier une liste de
    choix finis, séparés par le caractère `|` (pipe), à partir de laquelle
    l'utilisateur sélectionnera une valeur (e.g. `X|FOO|BAR`).

volatile (*optionnel*)
:   `Y` (pour yes) ou `N` (pour no), permet de spécifier si la valeur entrée
    doit être supprimée après l'installation ou upgrade.
    
    Cela permet d'indiquer que la valeur n'est pas mémorisée, et donc qu'elle
    sera demandée avec sa valeur par défaut à chaque upgrade du module par
    exemple.
    
    Valeur par défaut : `N`.

oninstall (*optionnel*)
:   Permet de spécifier la visibilité du paramètre lors d'une installation du
    module (`R` pour lecture seule, `W` pour lecture/écriture, `H` pour caché).
    
    Valeur par défaut : `W`.

onedit (*optionnel*)
:   Permet de spécifier la visibilité du paramètre lors de l'édition des
    paramètres depuis la liste des modules installés.
    
    Valeur par défaut : `R`.

onupgrade (*optionnel*)
:   Permet de spécifier si le paramètre est re-demandé lors des upgrades du
    module.
    
    Valeur par défaut : `H`.

Exemple :

    [xml]
    <parameters>
      <param name="foo_dir" label="Directory of FOO" type="text" default="/var/foo" needed="Y" />
      <param name="foo_color" label="Color of FOO" type="enum" values="red|green|blue" default="green" needed="N" />
    </parameters>

## Opération d'install/upgrade/archive/restore/delete et phases {#module-wf-install-upgrade}

Lors de l'*opération* d'installation ou d'upgrade d'un module, ou
d'archivage/restauration/suppression d'un contexte, un ensemble de *phases*
contenant des *process* sont déroulés.

Les *phases* sur lesquelles vous pouvez spécifier vos *process* sont identifiées
en couleur dans le diagramme ci-dessous.

![Workflow install/upgrade d'un module](module-install-upgrade-workflow.png)

Chaque *opération* (install, upgrade, etc.) comporte des *phases* qui sont
exécutés à un moment du traitement du module

L'opération d'installation comporte ainsi deux phases sur lesquelles vous
pouvez spécifier vos process : une phase avant l'installation des fichiers du
module (phase [`pre-install`][node_pre-install]), et une phase après
l'installation des fichiers du module
(phase [`post-install`][node_post-install]).

De la même manière, lors de l'upgrade : une phase avant la suppression des
anciens fichiers du module et l'installation des nouveaux fichiers du module
(phase [`pre-upgrade`][node_pre-upgrade]), et une phase après l'installation
des nouveaux fichiers du module (phase [`post-upgrade`][node_post-upgrade]).

Chaque phase ([`pre-install`][node_pre-install],
[`post-install`][node_post-install], etc.) spécifie un ensemble de process
(éléments [`<check>`][node_check], [`<process>`][node_process] ou
[`<download>`][node_download]) qui sont exécutés et qui retournent un status
d'échec ou de réussite.

Une phase est validée lorsque tous ses sous-éléments process ont retourné un
statut de réussite.

Si une phase n'est pas validée, alors les messages d'erreurs rencontrés sont
présentés à l'utilisateur , et celui-ci peut rejouer le process après avoir
éventuellement corrigé le problème, ou bien il peut choisir d'ignorer les
messages d'erreurs et poursuivre le déroulé de la phase.

Les process possibles dans les phases sont des éléments :

* [`<check>`][node_check]
* [`<process>`][node_process]
* [`<download>`][node_download]

Chaque proccess peut fournir un élément `<label>` présenté comme titre dans la
liste des process de la phase, et un `<help>` qui sera présenté à l'utilisateur
lorsque l'action échoue.

En l'absence de `<label>` et de `<help>` un message générique est composé pour
identifier le process.

### Phase pre-install {#manex-ref:a57ff321-d00f-4f55-ba88-6633a686d856}

Les process de `<pre-install>` s'exécutent avant l'installation des fichiers du
module sur le système de fichier.

Exemple :

    [xml]
    <pre-install>
      <check type="phpfunction" function="pspell_new">
        <label>Vérification du support de pspell par PHP.</label>
        <help>Il faut peut-être installer php5-pspell avec aspell et aspell-fr</help>
      </check>
      <check type="syscommand" command="convert" />
    </pre-install>

Les process de `<pre-install>` serviront généralement à vérifier la présence de
certains éléments et à bloquer l'installation si ces éléments ne sont pas
présents/corrects.

### Phase post-install {#manex-ref:ced835d9-ce84-420d-850a-dc655cd203fa}

Les process de `<post-install>` s'exécutent après l'installation des fichiers du
module sur le système de fichier.

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

Les process de `<post-install>` serviront généralement à configurer le module
qui vient d'être installé. Une erreur dans la phase de post-install laissera les
fichiers installés en place, mais le paquet sera marqué en erreur de
post-install dans l'interface.

### Phase pre-upgrade {#manex-ref:894dd826-0669-4ce1-aef5-866b4f9b612f}

Les process de `<pre-upgrade>` s'exécutent avant l'installation des nouveaux
fichiers du module sur le système de fichiers.

Exemple :

    [xml]
    <pre-upgrade>
      <check type="phpfunction" function="pspell_new">
        <help>Il faut peut-être installer php5-pspell avec apspell et aspell-fr</help>
      </check>
      <check type="syscommand" command="convert" />
    </pre-upgrade>

Les process de `<pre-upgrade>` serviront généralement à vérifier la présence de
certains éléments et bloquer l'upgrade si ces éléments ne sont pas
présents/corrects.

### Phase post-upgrade {#manex-ref:ccd2c732-1932-4f8c-bf1c-7311dc48f1de}

Les process de `<post-upgrade>` s'exécutent après l'installation des nouveaux
fichiers du module sur le système de fichier.

Exemple :

    [xml]
    <post-upgrade>
      <process command="programs/pre_migration FOO">
        <label>Pre-migration scripts</label>
      </process>
      <process command="programs/record_application FOO">
        <label>Update application record in database</label>
      </process>
      <process command="programs/post_migration FOO">
        <label>Post-migration scripts</label>
      </process>
      <process command="programs/update_catalog">
        <label>Re-generate localization catalog</label>
      </process>
    </post-upgrade>

Les process de `<post-upgrade>` serviront généralement à configurer le module
qui vient d'être installé, lancer les scripts de migration, etc. Une erreur dans
la phase de post-upgrade laissera les fichiers installés en place, mais le
paquet sera marqué en erreur de post-upgrade dans l'interface.

### Phase reconfigure {#manex-ref:d3679ce1-5475-4de2-a816-b399d881e5c0}

Les process de `<reconfigure>` s'exécutent après la restauration d'un contexte
depuis une archive.

Les process possibles sont les mêmes que pour les phases de
[`<post-install>`][node_post-install] ou [`<post-upgrade>`][node_post-upgrade].

### Phase pre-archive {#manex-ref:d31adf2b-c2c6-4536-8473-8cbd415f9228}

Les process de `<pre-archive>` s'exécutent avant l'archivage d'un contexte.

Les process possibles sont les mêmes que pour les phases de
[`<post-install>`][node_post-install] ou [`<post-upgrade>`][node_post-upgrade].

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure d'archivage.

### Phase post-archive {#manex-ref:455076b2-2265-43cb-9258-4b18006025e7}

Les process de `<post-archive>` s'exécutent après l'archivage du contexte.

Les process possibles sont les mêmes que pour les phases de
[`<post-install>`][node_post-install] ou [`<post-upgrade>`][node_post-upgrade].

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure d'archivage.

### Phase pre-restore {#manex-ref:4f34f078-29f8-4097-8bcd-2a429d20c1b6}

La phase de `<pre-restore>` n'est pas utilisable car les modules n'existent
pas encore dans le contexte.

### Phase post-restore {#manex-ref:0b92006c-f874-4cbb-977c-d8c67c0935a8}

Les process de `<post-restore>` s'exécutent après la restauration d'un contexte à
partir d'un archive et après l'exécution de la phase de
[reconfigure][reconfigure].

Les process possibles sont les mêmes que pour les phases de
[`<post-install>`][node_post-install] ou [`<post-upgrade>`][node_post-upgrade].

Le status d'échec/erreur n'est pas pris en compte et ne bloque pas la
procédure de restauration.

### Phase pre-delete {#manex-ref:e132707d-8080-4d9d-90b4-3fa73dcec0e9}

Les process de `<pre-delete>` s'exécutent avant la suppression d'un contexte.

Les process possibles sont les mêmes que pour les phases de
[`<post-install>`][node_post-install] ou [`<post-upgrade>`][node_post-upgrade].

Le status d'échec/erreur est pris en compte. Lorsqu'un process de `<pre-
delete>` est mis en échec, l'utilisateur a alors le choix de rejouer le
process ou bien de poursuivre l'exécution.

### Phase post-delete {#manex-ref:181efd01-521d-492e-8893-5596c696a778}

La phase de `<post-delete>` n'est pas utilisable car les modules n'existent
plus suite à la suppression du contexte.

## Les process de phase {#manex-ref:66ae8fce-e24c-4e1c-b825-fe0de5d12aee}

### Process `<check>` {#manex-ref:dd6c4985-b5eb-4a0e-a4fb-d3ec067cfa2d}

Les process `<check>` permettent d’exécuter des actions pour vérifier la
présence de certains éléments.

Un process `<check>` peut être déclaré optionnel (attribut `optional="Y"`)
auquel cas l'utilisateur aura la possibilité d'outrepasser le check s'il est mis
en erreur. Dans le cas contraire, un `<check>` en erreur ne peut pas être
outrepassé.

Le process `<check>` supporte plusieurs types de vérifications qui sont
spécifiées via l'attribut `type` qui peut prendre les valeurs suivantes :

phpfunction
:   Le check de type `phpfunction` permet de vérifier la présence d'une fonction
    PHP.
    
    Le nom de la fonction testée est spécifié avec l'attribut `function`.
    
    Exemple :

    [xml]
    <check type="phpfunction" function="pg_connect" />

syscommand
:   Le check de type `syscommand` permet de vérifier la présence d'une commande
    disponible sur le système.
    
    Le nom de la commande testée est spécifié avec l'attribut `command`.
    
    Exemple :

    [xml]
    <check type="syscommand" command="convert" optional="Y">
      <help>C'est bien si vous avez cette commande, mais on peut faire sans...</help>
    </check>

phpclass
:   Le check de type `phpclass` permet de vérifier la présence d'une classe objet PHP.
    
    Le nom de la classe PHP est spécifié avec les attributs suivants :
    
    * `include` : le nom du fichier pour inclure la définition de la classe
    * `class` : le nom de la classe
    
    Exemple :

    [xml]
    <check type="phpclass" include="Net/SMTP.php" class="Net_SMTP" />

apachemodule
:   Le check de type `apachemodule` permet de vérifier qu'un module Apache particulier est activé et chargé par celui-ci.
    Le nom du module est spécifié par l'attribut `module`.
    
    Attention :
    
    * Lors de l'installation par le CLI WIFF, ce check retourne
    toujours vrai.
    
    Exemple :

    [xml]
    <check type="apachemodule" module="mod_expires" />

exec
:   Le check de type `exec` permet d'exécuter une commande shell et vérifier son
    exit code. La commande à exécuter est spécifiée avec l'attribut `cmd`.
    
    L'interprétation du exit code suit la logique Unix avec :
    
    * exit code == `0` pour succès;
    * exit code != `0` pour échec.
    
    Note :
    
    * Le shell utilisé est le shell par défaut paramétré sur le système ou PHP
    (généralement `/bin/sh`).
    
    Exemple :

    [xml]
    <check type="exec" cmd="bash -c 'exit $(($RANDOM%2))'">
      <label>Do you feel lucky?</label>
      <help>Try again!</help>
    </check>

file
:   Le check de type `file` permet de vérifier l'existence ou le type d'un
    fichier ou répertoire.
    
    Les paramètres sont :
    
    * `file` : Le chemin du fichier ou du répertoire à tester.
    * `predicate` : L'opérateur de comparaison parmi la liste suivante :
        * `file_exists` (ou `e`, `-e`, `a` `-a`) : vrai si le fichier ou le répertoire existe.
        * `is_dir` (ou `d`, `-d`) : vrai si `file` est un répertoire.
        * `is_file` (ou `f`, `-f`) : vrai si `file` est un fichier.
        * `is_link` (ou `L`, `-L`) : vrai si `file` est un lien symbolique.
        * `is_readable` (ou `r`, `-r`) : vrai si `file` est lisible.
        * `is_writable` (ou `w`, `-w`) : vrai si `file` est inscriptible.
        * `is_executable` (ou `x`, `-x`) : vrai si `file` est exécutable (pour un répertoire cela correspond d'être traversé).
    
    Exemple :

    [xml]
    <check type="file" file="/tmp" predicate="is_dir">
        <label>Test si /tmp est bien un répertoire</label>
        <help>/tmp n'existe pas ou n'est pas un répertoire valide.</help>
    </check>
    <check type="file" file="/bin/bash" predicate="is_executable" />

pgversion
:   Le check de type `pgversion` permet de vérifier la version du serveur
    PostgreSQL de base de données.
    
    Les paramètres sont :
    
    * `service` : Le nom du service d'accès à la base de données.
    * `predicate` : L'opérateur de comparaison :
        * `lt` : strictement inférieur ;
        * `le` : inférieur ou égal ;
        * `gt` : strictement supérieur ;
        * `ge` : supérieur ou égal ;
        * `eq` : égal ;
        * `ne` : non-égal (différent)
    * `version` : La version avec laquelle est effectuée la comparaison.

pgempty
:   Le check de type `pgempty` est vrai si la base de données référencée par le
    service est vide.
    
    Les paramètres sont :
    
    * `service` : Le nom du service d'accès à la base de données.

phpbug40926
:   Le check de type `phpbug40926` est vrai si PHP n'est pas affecté par le bug
    [#40926](https://bugs.php.net/bug.php?id=40926).

phpbug45996
:   Le check de type `phpbug45996` est vrai si PHP n'est pas affecté par le bug
    [#45996](https://bugs.php.net/bug.php?id=45996)

### Process `<process>` {#manex-ref:796c1c2a-95b8-4bd6-86dd-4ecada2ea22d}

Les process `<process>` servent à exécuter des commandes/programmes permettant
d'effectuer les opérations nécessaires au fonctionnement du module suite à son
installation.

Si le programme référencé par la propriété `command` commence par un `/`
(slash), alors le chemin du programme est considéré comme étant absolu. Dans
le cas contraire, le chemin du programme est préfixé avec le chemin de la
racine du contexte.

Exemples :

    [xml]
    <process command="programs/foo arg1 arg2 arg3" />

La commande exécutée sera `${WIFF_CONTEXT_ROOT}/programs/foo` avec les
arguments `arg1`, `arg2` et `arg3`.

    [xml]
    <process command="/usr/local/bin/foo arg1 arg2 arg3" />

La commande exécutée sera `/usr/local/bin/foo` avec les arguments `arg1`,
`arg2` et `arg3`.

Une ensemble de programmes sont livrés par `dynacase-core` dans le sous-
répertoire `programs/` du contexte, et leur fonctionnement est décrit dans le
[manuel de référence de Dynacase][dynacase-doc-core-reference].

#### Programmes personnalisés {#manex-ref:afc3d392-bd87-418a-af0e-ceb8924e74a2}

Vous avez la possibilité d'écrire vos propres programmes de post-install,
post-upgrade, etc. afin d'effectuer des opérations spécifiques à votre module.

Ces programmes seront généralement développés soit en shell Bash soit en PHP.
Ils sont disponibles après la phase de décompression de votre paquet, dans le
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

##### Écrire un programme personnalisé en shell Bash {#manex-ref:91aa0dd8-af31-4ade-892c-1ac64c7545e6}

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

##### Écrire un programme personnalisé en PHP {#module-programme-perso}

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

### Process `<download>` {#manex-ref:79df376c-b15c-4585-9066-5da67dc798dd}

Les process `<download>` servent à télécharger un fichier et exécuter un
traitement sur le fichier téléchargé. Il est utilisé par exemple pour
télécharger du code tiers livré sous forme d'archive Zip ou Tar.gz, et de
l'installer dans le contexte.

Exemple :

    [xml]
    [...]
    <parameters>
      <param name="foo_url" label="Foo download URL" type="text" default="http://www.example.net/foo/foo-1.0.0.zip" volatile="Y" />
    </parameters>
    
    <post-install>
      <download href="@{foo_url}" action="programs/foo_install">
        <label lang="en">Downloading and installing Foo</label>
        <help>An error occured while downloading or installing Foo...</help>
      </download>
    </post-install>
    [...]

L'élément download est généralement couplé à un paramètre qui permet de
demander et modifier l'URL de la ressource à télécharger.

Les propriétés suivantes sont utilisables sur l'élément download :

href
:   contient l'URL de la ressource à télécharger, ou bien la valeur
    d'un paramètre demandé précédemment avec la notation `@{nom_du_parametre}`.

action
:   contient le chemin d'un script qui sera exécuté avec le fichier
    téléchargé comme argument (e.g. `programs/foo_install /tmp/foo_url_xxx`).
    
    L'environnement d'exécution est identique à celui décrit dans la section
    [Programmes personnalisé](#module-programme-perso) ci-dessus.

## Variables dans les process de phase {#manex-ref:3a0d54fd-8005-4f1b-98e9-89df67930c1f}

Certaines propriétés de process de phase peuvent contenir des variables.

Ces variables permettent alors de référencer et d'utiliser la valeur d'un
paramètre de module.

La liste des process et des propriétés supportant l'évaluation des variables est
décrite ci-dessous.

### Notation {#manex-ref:3682b0bb-0ab7-4ea8-90c4-4b6af58f150c}

Les variables peuvent être exprimées avec les notations suivantes :

* `@PARAM_NAME`
* `@{PARAM_NAME}`

Pour entrer un caractère `@` literal il faut doubler le caractère :

* `@@` → `@`

Les noms des variables doivent être de la forme `[a-zA-Z_][a-zA-Z0-9_]*`.

### Process supportant les variables {#manex-ref:81c7354d-2585-4cbf-a770-c5e3b70a9fca}

#### Process `<download>` {#manex-ref:b4476cf1-5bcf-44b4-8e4f-a8e200f54abf}

Dans un process `<download>`, les propriétés suivantes supportent l'évaluation
des variables :

* `href`

Exemple :

    [xml]
    <download href="... @{PARAM_NAME} ..." />

#### Process `<process>` {#manex-ref:132a458a-2fd4-4a9b-847d-e44a0e48a870}

Dans un process `<process>`, les propriétés suivantes supportent l'évaluation
des variables :

* `command`

Exemple :

    [xml]
    <process command="... @{PARAM_NAME} ..." />

#### Process `<check>` de type `exec` {#manex-ref:461dd848-f425-4b8d-80ce-eaeb63cba2a5}

Dans un process `<check>` de type `exec`, les propriétés suivantes supportent
l'évaluation des variables :

* `cmd`

Exemple :

    [xml]
    <check type="exec" cmd="... @{PARAM_NAME} ..." />

## Validation XML {#manex-ref:ce6612b8-3c99-41c2-a21d-a6ea23ab8c06}

Le format du fichier `info.xml` peut être validé afin de s'assurer qu'il ne
contient pas d'erreurs dans sa structure.

Pour cela, vous pouvez utiliser le fichier de définition
[`webinst-module-1.0.xsd`][xsd] et la commande [`xmllint`][xmllint] comme suit :

    $ xmllint --schema webinst-module-1.0.xsd info.xml

## Changements par rapport à l'ancien format {#manex-ref:8005debe-6796-4c93-bf66-78889b153bfb}

Liste des éléments supprimés/non-supportés/modifiés par rapport à l'ancien
format :

* L'attribut "`infopath`" de l'élément racine [`<module/>`][node_root_module]
n'est plus supporté.

* L'attribut "`disabled`" de l'élément racine [`<module/>`][node_root_module]
n'est plus supporté.

* L'attribut "`author`" de l'élément racine [`<module/>`][node_root_module]
n'est plus supporté.

* L'élément `<changelog/>` de l'élément racine [`<module/>`][node_root_module]
n'est plus supporté et peut être remplacé par l'utilisation de l'attribut
"`changelog`" sur l'élément racine [`<module/`][node_root_module] (contient
une URL vers le changelog du module).

* L'attribut "`lang`" de l'élément [`<description/>`][node_description] n'est
plus supporté (par conséquent un seul élément
[`<description/>`][node_description] est autorisé dans l'élément racine
[`<module/>`][node_root_module]).

* L'attribut "`lang`" des éléments `<label/>` et `<help/>` n'est plus supporté
(par conséquent un seul élément `<label/>` et un seul élément `<help/>` est
autorisé dans les éléments [`<process/>`][node_process],
[`<check/>`][node_check] et [`<download/>`][node_download]).

* L'attribut "`basecomponent`" de l'élément racine
[`<module/>`][node_root_module] doit contenir les valeurs "`Y`" ou "`N`".

* L'attribut "`volatile`" de l'élément [`<param/>`][node_params] doit contenir
les valeurs "`Y`" ou "`N`".

* L'attribut "`optional`" des éléments [`<process/>`][node_process] et
[`<check/>`][node_check] doit contenir les valeurs "`Y`" ou "`N`".

<!-- links -->
[gnu_tar]: https://www.gnu.org/software/tar/
[gzip]: http://www.gzip.org/
[info_xml]: #manex-ref:22c8b9e1-1a44-42e9-bc6e-68f3373beac6
[reconfigure]: #manex-ref:d3679ce1-5475-4de2-a816-b399d881e5c0
[changes]: #manex-ref:8005debe-6796-4c93-bf66-78889b153bfb
[node_root_module]: #manex-ref:d168e9ea-78dc-4e23-b2a6-814bee0e4e2e
[node_params]: #manex-ref:3b643797-d2e5-4906-9c81-a6e59bd93bbd
[node_process]: #manex-ref:796c1c2a-95b8-4bd6-86dd-4ecada2ea22d
[node_check]: #manex-ref:dd6c4985-b5eb-4a0e-a4fb-d3ec067cfa2d
[node_download]: #manex-ref:79df376c-b15c-4585-9066-5da67dc798dd
[node_description]: #manex-ref:aa5e19bc-1809-42d2-a165-98736747b1da
[node_label]: #module-wf-install-upgrade
[node help]: #module-wf-install-upgrade
[node_pre-install]: #manex-ref:a57ff321-d00f-4f55-ba88-6633a686d856
[node_post-install]: #manex-ref:ced835d9-ce84-420d-850a-dc655cd203fa
[node_pre-upgrade]: #manex-ref:894dd826-0669-4ce1-aef5-866b4f9b612f
[node_post-upgrade]: #manex-ref:ccd2c732-1932-4f8c-bf1c-7311dc48f1de
[node_reconfigure]: #manex-ref:d3679ce1-5475-4de2-a816-b399d881e5c0
[node_pre-archive]: #manex-ref:d31adf2b-c2c6-4536-8473-8cbd415f9228
[node_post-archive]: #manex-ref:455076b2-2265-43cb-9258-4b18006025e7
[node_pre-restore]: #manex-ref:4f34f078-29f8-4097-8bcd-2a429d20c1b6
[node_post-restore]: #manex-ref:0b92006c-f874-4cbb-977c-d8c67c0935a8
[node_pre-delete]: #manex-ref:e132707d-8080-4d9d-90b4-3fa73dcec0e9
[xsd]: https://github.com/Anakeen/dynacase-xml-schemas/blob/master/webinst-module-1.0.xsd
[xmllint]: http://xmlsoft.org/xmllint.html
[dynacase-doc-core-reference]: /dynacase/3.2/dynacase-doc-core-reference/website/book/index.html
[php_version_compare]: http://www.php.net/manual/function.version-compare.php
