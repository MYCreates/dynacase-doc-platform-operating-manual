# Migration vers 4 {#manex-ref:aa3e784e-59d0-47ca-ab74-4d57ea360f91}

## Depuis une version 3.1 {#manex-ref:3fd49563-a822-4477-ae2d-0a85db0a101c}

L'upgrade en version 4 depuis une version 3.1 (ou inférieure) n'est pas supportée, et nécessite donc au préalable de passer en version 3.2 (voir documentation [Installation & Exploitation Dynacase Platform 3.2][migration_31_32]).

## Depuis une version 3.2 {#manex-ref:ed1a4260-0a41-4598-aafd-78cb8d96e79b}

### PostgreSQL 9.3

Dynacase Platform 4 nécessite PostgreSQL `9.3`.

Si votre installation de Dynacase Platform 3.2 utilise un serveur PostgreSQL <= `9.2`, il faudra alors au préalable transférer la base de donnée sur un serveur PostgreSQL `9.3`.

Cette opération de transfert peut-être faite à l'aide des commandes `pg_dump` (ou `pg_dumpall`) et `pg_restore` de PostgreSQL.

Voir documentation [Upgrading a PostgreSQL Cluster][pg93upgrading] pour les procédures de mise à jour en PostgreSQL `9.3`.

<!-- links -->
[migration_31_32]: /dynacase/3.2/dynacase-doc-platform-operating-manual/website/book/manex-ref:aa3e784e-59d0-47ca-ab74-4d57ea360f91.html
[pg93upgrading]: http://www.postgresql.org/docs/9.3/static/upgrading.html