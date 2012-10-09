# Migration vers 3.2

## Depuis une version 3.0

L'upgrade en version 3.2 depuis une version 3.0 n'est pas supportée, et nécessite donc au pré-alable de passer en version 3.1.

## Depuis une version 3.1

### Import de familles/documents

#### Utilisation de `importDocuments`
L'import des familles et des documents doit être effectué avec le script d'API `importDocuments`, qui remplace l'ancien script d'import `freedom_import`, et effectue un contrôle plus strict et précis sur les éléments importés.

Vous devez donc modifier vos fichiers `info.xml`, et/ou vos procédures d'import, pour utiliser `importDocuments` à la place de `freedom_import`.

#### Structure fichiers ODS

L'import de vos familles peut nécéssiter un découpage de votre fichier ODS en sous-fichiers ODS autonomes en termes de dépendances, et importer ceux-ci dans l'ordre adéquat.

En effet, les contrôles plus stricts imposés lors de l'import, interdisent les références à des éléments qui n'existent pas. Il vous faut donc importer vos éléments dans le bon ordre.

Pour cela, nous préconisons le découpage suivant :

1. Un fichier pour la structure de vos familles qui contient les attributs, le paramétrage des attributs, mais ne contient pas de références aux profils (CPROFID, DPROFID), aux cycles (WID), etc.
2. Un fichier d'import des documents qui contient l'import des documments comme les profils et les cycles.
3. Un fichier de paramétrage qui va appliquer le paramétrage des profils, des cycles, etc. sur les familles précédemment importées.

### Fichiers méthodes de famille

Un contrôle plus strict est applliqué sur les fichiers méthodes.

#### Déclaration explicite `class X extends Y`

Tous les fichiers de méthodes doivent être ré-écrit sous la forme d'une classe PHP standard, dérivant de la classe d'une autre famille ou de la classe "Doc", à l'aide des tags de commentaires `@begin-method-ignore` et `@end-method-ignore`.

Exemple :

    [php]
    /**
     * @begin-method-ignore
     */
    class _MA_FAMILLE extends Doc {
    /**
     * @end-method-ignore
     */

        public function postModify() {
            [...]
        }

        [...]

    /**
     * @begin-method-ignore
     */
    }
    /**
     * @end-method-ignore
     */

#### Méthodes de contrôle de vue (`@templateController`)

Les méthodes utilisées comme contrôleur de vue doivent être déclarées avec un tag de commentaire `@templateController`.

Le tag `@templateController` permet de déclarer explicitement les méthodes utilisables comme contrôleur de vue, et d'interdire l'utilisation de toute autre méthode.

Exemple :

    [php]
    /**
     * @begin-method-ignore
     */
    class _MA_FAMILLE extends Doc {
    /**
     * @end-method-ignore
     */

        [...]

        /**
         * @templateController
         */
        public function maVue() {
            [...]
        }

        [...]

    /**
     * @begin-method-ignore
     */
    }
    /**
     * @end-method-ignore
     */

#### Méthodes exécutables par FDL_METHOD (`@apiExpose`)

Les méthodes qui sont exécutées via l'action FDL_METHOD doivent être déclarées avec un tag de commentaire `@apiExpose`.

Le tag `@apiExpose` permet de déclarer explicitement les méthodes utilisables par FDL_METHOD, et d'interdire l'utilisation de toute autre méthode.

Exemple :

    [php]
    /**
     * @begin-method-ignore
     */
    class _MA_FAMILLE extends Doc {
    /**
     * @end-method-ignore
     */

        [...]

        /**
         * @apiExpose
         */
        public function doSomethingOnDocument() {
            [...]
        }

        [...]

    /**
     * @begin-method-ignore
     */
    }
    /**
     * @end-method-ignore
     */

#### Méthodes utilisables comme critère de recherche (`@searchLabel`)

Les méthodes utilisables comme critère de recherche doivent être déclarées avec un tag de commentaire `@searchLabel` et `@searchType`.

> ☞ Voir chapitre "3.3.9. Utilisation de méthodes dans l'interface de recherche détaillée" du manuel de référence.

Exemple :

    [php]
    /**
     * @begin-method-ignore
     */
    class _MA_FAMILLE extends Doc {
    /**
     * @end-method-ignore
     */

        [...]

        /**
         * @searchLabel Température extérieure
         * @searchType double
         */
        public function exteriorTemperature() {
            return TemperatureSensor::getTemperature();
        }

        [...]

    /**
     * @begin-method-ignore
     */
    }
    /**
     * @end-method-ignore
     */

### Droits négatifs

Les droits négatifs ne sont plus supportés.

Si vous avez utilisé les droits négatifs (boules rouge dans les profils) il vous faudra ré-écrire ceux-ci en utilisant la notion de rôles.

La structure de la table `docperm` et les interfaces de saisie des droits ont été modifiés pour prendre en compte ce nouveau fonctionnement.

> ☞ Voir chapitres "2.2. Gestion des utilisateurs" et "2.3. Sécurité" du manuel de référence.

Nous préconisons de ne plus gérer les droits par des groupes mais de se baser sur le système des rôles.

### Familles du "Carnet d'adresse" (USER, SOCIETY et SITE)

Les familles `USER`, `SOCIETY` et `SITE` sont désormais fournies par le module `dynacase-contact`.

Le lien de parenté entre `IUSER` et `USER` n'existe plus, et `IUSER` devient à présent une famille "top-level" sans parents.

Lors de la migration, l'application "Carnet d'adresse" est désactivée. Pour la ré-activer, il vous faudra installer le module `dynacase-contact`.

### Envoi de mails

Par défaut, les adresses de courriel des utilisateurs ne sont plus visible pour l'envoi de courriels (par l'icône "enveloppe" par exemple). Seules les familles déclarés avec un tag `MAILRECIPIENT` sont utilisables pour l'envoi de courriels.

Pour ré-activer l'ancien fonctionnement sur les `ISUER` (et `LDAPUSER`) il vous faudra mettre à jour ces familles pour positionner ce tag `MAILRECIPIENT`.

> ☞ Voir chapitre "2.1.9. Déclarer une famille comme destinataire de courriel" du manuel de référence.

### Valeur par défaut DEFAULT

Par le passé (version < 3.2), lors de la déclaration d'une famille, la déclaration d'une valeur par défaut sur un attribut d'une famille mère n'avait pas de répercussion sur la valeur par défaut de cet attribut sur une famille fille.

A présent (version >= 3.2), la valeur par défaut d'un attribut d'une famille fille pourra être la valeur par défaut positionnée sur l'attribut de la famille mère, si la famille fille ne spécifie pas explicitement sa propre valeur par défaut.

La valeur par défaut d'un attribut remonte donc les liens de parenté à la recherche d'une valeur par défaut pour cet attribut.

### Familles système

Vos familles de cycle de vie doivent être déclarés système à l'aide du mot-clef `USEFOR` avec la valeur `SW`.

> ☞ Voir chapitre "2.1.2.6.2. Caractéristiques générales" du menuel de référence pour l'utilisation du mot-clef `USEFOR`.

La classification, en familles système et familles non-système, des familles livrés par défaut a été modifié.

Les familles qui ont été basculées en familles système sont :

      id  |      name      | usefor
    ------+----------------+--------
        1 | BASE           | S
        3 | PDOC           | SP
        4 | PDIR           | SP
        5 | SEARCH         | S
        6 | PSEARCH        | SP
       16 | DSEARCH        | S
       18 | GUIDECARD      | SG
       19 | SGUIDECARD     | SG
       20 | WDOC           | SW
       23 | PFAM           | SP
       24 | TEXT           | S
       25 | REPORT         | S
       31 | MSEARCH        | S
       36 | BATCH          | SA
      127 | IGROUP         | S
      129 | GROUP          | S
      130 | ROLE           | S
     1001 | SENTMESSAGE    | S
     1006 | PUBLIMAIL      | SA
     1007 | BASICBATCH     | SA
     1019 | PORTAL_SERVICE | S
     1020 | USER_PORTAL    | S

Ces familles ne sont donc plus accessibles par défaut aux utilisateurs.

### Éditeur htmltext

L'éditeur HTML pour les attributs `htmltext` a été changé et est à présent [CKeditor](http://ckeditor.com/).
