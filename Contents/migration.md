# Migration vers 3.2 {#manex-ref:aa3e784e-59d0-47ca-ab74-4d57ea360f91}

## Depuis une version 3.0 {#manex-ref:3fd49563-a822-4477-ae2d-0a85db0a101c}

L'upgrade en version 3.2 depuis une version 3.0 n'est pas supportée, et nécessite donc au préalable de passer en version 3.1.

## Depuis une version 3.1 {#manex-ref:ed1a4260-0a41-4598-aafd-78cb8d96e79b}

### importation de familles/documents {#manex-ref:8975495c-e5cb-4980-b443-45df19fa06d1}

#### Utilisation de `importDocuments` {#manex-ref:736ede82-4170-439d-9289-3c8cf89e770a}
L'importation des familles et des documents doit être effectuée avec le script d'API `importDocuments`, qui remplace l'ancien script d'importation `freedom_import`, et effectue un contrôle plus strict et précis sur les éléments importés.

Vous devez donc modifier vos fichiers d'importation présents dans `info.xml`, et/ou vos procédures d'importation, pour utiliser `importDocuments` à la place de `freedom_import`.

#### Structure fichiers ODS {#manex-ref:e15683c7-c234-4d10-8752-e7d24c2a1378}

L'importation de vos familles peut nécessiter un découpage de votre fichier ODS/CSV en sous-fichiers ODS/CSV autonomes en termes de dépendances, et importer ceux-ci dans l'ordre adéquat.

En effet, les contrôles plus stricts imposés lors de l'importation, interdisent les références à des éléments qui n'existent pas. Il vous faut donc importer vos éléments dans l'ordre de dépendance.

Pour cela, nous préconisons le découpage suivant :

1. Un fichier pour la structure de vos familles qui contient les attributs, le paramétrage des attributs, mais ne contient pas de références aux profils (CPROFID, DPROFID), aux cycles (WID), etc.
2. Un fichier d'importation des documents systèmes qui contient l'importation des documents comme les profils et les cycles.
3. Un fichier de paramétrage qui va appliquer le paramétrage des profils, des cycles, etc. sur les familles précédemment importées.

### Fichiers méthodes de famille {#manex-ref:4b132a53-9500-4423-8470-7ee4513d8bfd}

Un contrôle plus strict est appliqué sur les fichiers méthodes.

#### Déclaration explicite `class X extends Y` {#manex-ref:680039fb-ca38-4358-ba3c-ecc9ffa14e6e}

Tous les fichiers de méthodes doivent être réécrits sous la forme d'une classe PHP standard, dérivant de la classe d'une autre famille ou de la classe "Doc", à l'aide des tags de commentaires `@begin-method-ignore` et `@end-method-ignore`.

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

#### Méthodes de contrôle de vue (`@templateController`) {#manex-ref:0504ea31-72e9-4745-8895-0e1740b87c5a}

Les méthodes utilisées comme contrôleur de vue doivent être déclarées avec un tag de commentaire `@templateController`. Dans le cas contraire, un message d'erreur sera affiché à la place de la vue.

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

#### Méthodes exécutables par FDL_METHOD (`@apiExpose`) {#manex-ref:97b26154-1014-4506-968b-3c87f1f10234}

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

Cette notation est aussi obligatoire pour les menus déclarant un appel de la forme `::myMethod()`. Dans ce cas, la méthode `myMethod` doit avoir aussi le tag `@apiExpose`.

#### Méthodes utilisables comme critère de recherche (`@searchLabel`) {#manex-ref:fd45fb4a-2003-4ee0-b8ab-c76e1ce3b17a}

Les méthodes utilisables comme critère de recherche doivent être déclarées avec un tag de commentaire `@searchLabel` et `@searchType`.

> ☞ Voir chapitre [17.8.2.2 Utilisation de méthodes dans l'interface de recherche détaillée](http://docs.anakeen.com/dynacase/3.2/dynacase-doc-core-reference/website/book/core-ref:b0bc6976-8eb3-4ba0-9999-241ecf7b1682.html#core-ref:b318bafc-adf9-4ebc-802d-0cb1a8f82054).

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

### Droits négatifs {#manex-ref:60039751-3aad-4197-b4ad-81d152011aed}

Les droits négatifs ne sont plus supportés.

Si vous avez utilisé les droits négatifs (boules rouges dans les profils) il vous faudra réécrire ceux-ci en utilisant la notion de rôles.

La structure de la table `docperm` et les interfaces de saisie des droits ont été modifiés pour prendre en compte ce nouveau fonctionnement.

> ☞ Voir chapitres [Comptes : utilisateurs, groupes et rôles](http://docs.anakeen.com/dynacase/3.2/dynacase-doc-core-reference/website/book/core-ref:f6d55bb1-7254-480e-9bfa-61f5e532ccad.html) et [Sécurité : authentification, droit applicatif, droit documentaire](http://docs.anakeen.com/dynacase/3.2/dynacase-doc-core-reference/website/book/core-ref:4e298112-3c56-4677-a05f-e314b1406326.html).

Nous préconisons de ne plus gérer les droits par des groupes mais de se baser sur le système des rôles.

Pour savoir si vous avez des droits négatifs, vous pouvez utiliser la requête suivante :

    [sql]
    # SELECT * from docperm where unacl != 0;

S'il n'y a aucune ligne retournée, aucun droit négatif n'est posé.
### Familles du "Carnet d'adresses" (USER, SOCIETY et SITE) {#manex-ref:a9de2602-132a-41bb-92b0-7f394bcb112f}

Les familles `USER`, `SOCIETY` et `SITE` sont désormais fournies par le module `dynacase-contact`.

Le lien de parenté entre `IUSER` et `USER` n'existe plus, et `IUSER` devient à présent une famille "top-level" sans parents.

Lors de la migration, l'application "Carnet d'adresse" est désactivée. Pour la réactiver, il vous faudra installer le module `dynacase-contacts`.

### Envoi de mails {#manex-ref:5891cbb5-b38e-4465-9818-f956c5fec8dd}

Par défaut, les adresses de courriel des utilisateurs ne sont plus visibles pour l'envoi de courriels (depuis l'icône "enveloppe" présent sur les documents). Seules les familles déclarés avec un tag `MAILRECIPIENT` sont utilisables pour l'envoi de courriels.

Pour réactiver l'ancien fonctionnement sur les `IUSER` (et `LDAPUSER`) il vous faudra mettre à jour ces familles pour positionner ce tag `MAILRECIPIENT`.

> ☞ Voir chapitre [4.8.2.2 Définition de propriétés de famille](http://docs.anakeen.com/dynacase/3.2/dynacase-doc-core-reference/website/book/core-ref:cfc7f53b-7982-431e-a04b-7b54eddf4a75.html#core-ref:6f013eb8-33c7-11e2-be43-373b9514dea3) du manuel de référence.

Le module `dynacase-contacts` déclare la famille `USER` comme destinataire de courriel (il possède le tag `MAILRECIPIENT`).
### Valeur par défaut DEFAULT {#manex-ref:f38e0efc-81e5-4961-8308-7f593970412c}

Par le passé (version < 3.2), lors de la déclaration d'une famille, la déclaration d'une valeur par défaut sur un attribut d'une famille mère n'avait pas de répercussion sur la valeur par défaut de cet attribut sur une famille fille.

A présent (version >= 3.2), la valeur par défaut d'un attribut d'une famille fille pourra être la valeur par défaut positionnée sur l'attribut de la famille mère, si la famille fille ne spécifie pas explicitement sa propre valeur par défaut.

La valeur par défaut d'un attribut remonte donc les liens de parenté à la recherche d'une valeur par défaut pour cet attribut.

### Familles système {#manex-ref:c54177e2-eecd-4302-90ca-2a5642082969}

Vos familles de cycle de vie doivent être déclarés "système" à l'aide du mot-clef `USEFOR` avec la valeur `SW`.

> ☞ Voir chapitre [4.8.2.2 Définition de propriétés de famille](http://docs.anakeen.com/dynacase/3.2/dynacase-doc-core-reference/website/book/core-ref:cfc7f53b-7982-431e-a04b-7b54eddf4a75.html#core-ref:6f013eb8-33c7-11e2-be43-373b9514dea3) du manuel de référence.


La classification, en familles système et familles non-système, des familles livrées par défaut a été modifiée.

Les familles qui ont été basculées en familles "système" sont :

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

### Éditeur htmltext {#manex-ref:408812bc-e805-4784-8829-647d1e57ddd0}

L'éditeur HTML pour les attributs `htmltext` a été changé et est à présent [CKeditor](http://ckeditor.com/).
