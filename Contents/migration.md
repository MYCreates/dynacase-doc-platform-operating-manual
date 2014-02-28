# Migration 3.2 → 4 {#manex-ref:aa3e784e-59d0-47ca-ab74-4d57ea360f91}

_note:_ La mise à jour en version 4 depuis une version 3.1 (ou inférieure) n'est
pas supportée, et nécessite donc au préalable de passer en version 3.2 (voir
documentation [Installation & Exploitation Dynacase Platform
3.2][migration_31_32]).

## PostgreSQL 9.3

Si votre installation Dynacase Platform utilise un serveur PostgreSQL de version
inférieure à la `9.3`, il faudra alors au préalable transférer la base de donnée
sur un serveur PostgreSQL `9.3`.

Cette opération de transfert peut-être faite à l'aide des commandes `pg_dump`
(ou `pg_dumpall`) et `pg_restore` de PostgreSQL.

Voir documentation [Upgrading a PostgreSQL Cluster][pg93upgrading] pour les
procédures de mise à jour en PostgreSQL `9.3`.

## Incompatibilités 3.2 / 4

Les attributs multiples (attributs déclarés dans des tableaux ou ayant l'option
multiple) sont enregistrés sous la forme de [`array`][pgarray] dans la base de
données dans la version 4 de Dynacase à la place du type `text`.

Cette modification entraîne la restriction suivante : un attribut modifié
(MODATTR) ou redéfini dans une sous-famille ne peut pas changer le caractère
"multiple" de l'attribut originel.

Cette modification entraîne aussi une contrainte supplémentaire lié au type. Les
contrainte de type des différentes valeurs sont vérifiés aussi au niveau de la
base de données. Ces contraintes s'applique aux types "int", "double", "date",
"timestamp" et "time".


### Vérification d'aptitude pour les types multiples

Le premier point concernant le caractère multiple peut être vérifier depuis la
base de données en utilisant le script [validate4.sql][validate4].

Se connecter à la base de donnée (Dynacase 3.2) à l'aide de client `psql` et
interpréter le fichier "validate4.sql" qui aura été préalablement enregistré sur
le serveur.

La fonction `pg_temp.validateAttributeTypes()` retourne les attributs posant un
problème. La durée d'exécution de cette fonction peut prendre plusieurs minutes
en fonction du nombre de familles et d'attributs de votre système.

    dynacase=> \i /tmp/validate4.sql 
    CREATE FUNCTION
    CREATE FUNCTION
    CREATE FUNCTION
    CREATE FUNCTION
    CREATE FUNCTION
    CREATE FUNCTION
    dynacase=> select * from pg_temp.validateAttributeTypes();
               attrid           |    parentfamily    |         family       |                                   info                                    
    ----------------------------+--------------------+----------------------+---------------------------------------------------------------------------
     my_references              | MY_ABSTRACT_FAMILY | MY_PRODUCTS          | multiple mismatch : [177609, ismultiple: false]/[1925,ismultiple: true]
     my_deliverydate            | MY_ABSTRACT_FAMILY | MY_SOFTWARE_PRODUCTS | multiple mismatch : [177609, ismultiple: false]/[177630,ismultiple: true]
     my_furnishers              | MY_ABSTRACT_FAMILY | MY_PRODUCTS          | multiple mismatch : [177609, ismultiple: false]/[1925,ismultiple: true]
    (3 lignes)

Définition des colonnes :

`attrid`  
: nom de l'attribut dont une des redéfinitions est incompatible avec
l'attribut original.

`parentfamily`:
: identifiant de la famille d'origine décrivant l'attribut.

`family`:
: identifiant de la famille fille décrivant l'attribut incompatible.

`info`:
: Indique l'identifiant numérique de la famille d'origine et le caractère multiple
    ainsi que celui de la famille fille.

Pour corriger ce problème, il faut modifier l'option "multiple" ou le
rattachement à un tableau de l'attribut d'origine ou de l'attribut dérivé.


### Vérification d'aptitude pour les valeurs multiples

Le deuxième point concerne la vérification des valeurs d'attributs multiples.
Cette vérification peut être effectuée par la fonction
'pg_temp.validateMultipleValues()' fournie par le fichier [validate4.sql][validate4].


    dynacase=> \i /tmp/validate4.sql 
    dynacase=> select * from pg_temp.validateMultipleValues() where status = 'KO';
    
        attrid     | familyname | status |           info           |         docids         |   titles   |              wrongvalues               
    ---------------+------------+--------+--------------------------+------------------------+------------+----------------------------------------
     frst_z_int    | my_family  | KO     | wrong int detected       | {198649}               | {Z1}       | {"{a,20}"}
     frst_z_double | my_family  | KO     | wrong double detected    | {198649}               | {Z1}       | {"{a,4.98}"}
     frst_z_tz     | my_family  | KO     | wrong timestamp detected | {198652}               | {Z4}       | {"{030:67}"}
     frst_z_time   | my_family  | KO     | wrong time detected      | {198649,198651,198652} | {Z1,Z3,Z4} | {"{30:20,20:00}","{03:67}","{030:67}"}
    (4 lignes)


Définition des colonnes :


`attrid`  
: nom de l'attribut testé.

`familyname`  
: famille de l'attribut testé

`status`  
: statut du test : "OK" (test réussi), "KO" (problème de valeur), "SKIP" (test non pertinent pour cet attribut).

`info` 
: Message d'échec du test (KO) ou raison de non passage (SKIP).

`docid`
: tableau des identifiants des documents où les valeurs de l'attribut sont erronés.

`titles`
: tableau des titres des documents où les valeurs de l'attribut sont erronés.

`wrongvalues`:
: tableau des valeurs erronées.

Pour corriger ce problème, il faut modifier les documents indiqués afin d'avoir
des valeurs correctes. Ceci peut être fait par l'interface web si les documents
ne sont pas figés. S'ils sont figés, il faut modifier les valeurs directement en
base de données ou par programmation spécifique.


<!-- links -->
[migration_31_32]: /dynacase/3.2/dynacase-doc-platform-operating-manual/website/book/manex-ref:aa3e784e-59d0-47ca-ab74-4d57ea360f91.html
[pg93upgrading]: http://www.postgresql.org/docs/9.3/static/upgrading.html "Notice de mise à jour de postgresql"
[pgarray]:          http://www.postgresql.org/docs/9.3/static/arrays.html "Tableau postgresql"
[validate4]:        https://raw.github.com/Anakeen/dynacase-core/3.3-integration/App/Core/validate4.sql "Fonctions de vérification d'aptitude"