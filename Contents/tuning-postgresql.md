# Tuning PostgreSQL

Dans le cadre de son utilisation avec Dynacase, une couche d’abstraction logicielle Dynacase sur PostgreSQL permet d’optimiser son utilisation. Les éventuels tuning de la base PostgreSQL dépendent entièrement du modèle documentaire mis en œuvre par Dynacase. Ils sont réalisés ou préconisés si nécessaire lors de la mise en œuvre de la solution par un expert Anakeen.

## Configuration de PostgreSQL

La plupart des distributions livrent PostgreSQL avec une configuration basique, passe-partout, qui n'est pas forcement adaptée au volume et au nombre de transactions que peut générer Dynacase.
Voici quelques éléments de configuration pour une machine type avec 2 Go à 3 Go de RAM.

### shared_buffers

Le paramètre [shared_buffers](www.postgresql.org/docs/9.1/static/runtime-config-resource.html#GUC-SHARED-BUFFERS) permet de spécifier la taille de la mémoire partagée allouée par Postgresql.
Il est à noter que ce paramètre PostgreSQL dépend d'un paramètre du kernel qui indique la valeur maximale de mémoire partagée disponible pour les processus. Il faudra donc au préalable augmenter la valeur de ce paramètre au niveau du noyau, pour ensuite augmenter la valeur dans PostgreSQL.  
Sous Linux, la taille de la mémoire partagée maximale autorisée est définie par le paramètre sysctl kernel.shmmax :  

    [bash]
    # sysctl kernel.shmmax kernel.shmmax = 33554432

On voit dans ce cas que la valeur est de 32 Mo
Sur une machine dédiée à PostgreSQL avec 2 à 3 Go de RAM on alloue entre 1 et 2 Go de mémoire partagée utilisable par les process :  

    [bash]
     # sysctl -w kernel.shmmax=2147483648 kernel.shmmax = 2147483648

Pour rendre ce paramètre persistant au redémarrage, on enregistre celui-ci dans le fichier `/etc/sysctl.conf`.

Une fois le shmmax configuré sur le kernel, on définit le paramètres PostgreSQL shared_buffers.

    [bash]
    # vi /var/lib/pgsql/data/postgresql.conf
    […]
    shared_buffers = 1024MB
    […]

La prise en compte de ce paramètre est effective après re-démarrage de PostgreSQL :

    [bash]
    # /etc/rc.d/init.d/postgresql restart

Si Postgresql ne redémarre pas, vérifier que la valeur de *shared_buffers* est bien inférieure à la valeur de *kernel.shmmax*.

### effective_cache_size

Le paramètre [effective_cache_size](http://www.postgresql.org/docs/9.1/static/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) indique à PostgreSQL la mémoire restante une fois que PostgreSQL et tous les autres process du serveur tournent en fonctionnement normal. Cet espace "libre" est utilisé par le noyau pour gérer ses buffers et son cache. Cet espace est visualisable sous Linux avec la commande free sous la forme des valeurs de buffers et cached :

    [bash]
    # free
                 total       used       free    shared    buffers     cached
    Mem:       3095688    1932116    1163572         0      62524    1433776
    -/+ buffers/cache:     435816    2659872
    Swap:      1044208      89816     954392

Dans le cas ci-dessus, on peut donc indiquer à PostgreSQL une valeur de *effective_cache_size* de 1400 Mo :

    [bash]
    # vi /var/lib/pgsql/data/postgresql.conf
    […]
    effective_cache_size = 1400MB
    […]

### work_mem

Le paramètre [work_mem](http://www.postgresql.org/docs/9.1/static/runtime-config-resource.html#GUC-WORK-MEM) spécifie la quantité de mémoire que peut utiliser un process PostgreSQL pour effectuer des tris. Une valeur plus grande lui permettra de pouvoir manipuler plus de données en mémoire vive (plutôt que d'avoir recours à des fichiers temporaires qui sont plus lents).  
Ce paramètre doit être calibré en fonction du nombre maximum de connexions concurrentes déclaré par le paramètre *max_connections*. La mémoire allouée pour ces opérations étant alors égale à *max_connections* * *work_mem*.

    [bash]
    # vi /var/lib/pgsql/data/postgresql.conf
    […]
    max_connections = 64
    […]
    work_mem = 16Mb
    […]

Dans l'exemple ci-dessus, l'utilisation mémoire pourra monter à 64 * 16 Mo = 1 Go.

### maintenance_work_mem

Le paramètre [maintenance_work_mem](http://www.postgresql.org/docs/9.1/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) spécifie la quantité de mémoire que peuvent utiliser les commandes/process PostgreSQL de VACUUM, création d'index et altération de table. Une valeur plus grande lui permettra de pouvoir manipuler plus de données en mémoire vive, et accélèrera le temps de traitement sur les VACUUM (freedom_clean) par exemple.

    [bash]
    vi /var/lib/pgsql/data/postgresql.conf
    […]
    maintenance_work_mem = 128Mb
    […]

### Conclusion

Ces paramètres permettent donc de donner une plus grande liberté de mouvement à PostgreSQL comparés à ceux livrés par défaut.
Les valeurs données ci-dessus sont des exemples qu'il faudra bien sûr adapter/moduler en fonction de la machine et de son utilisation dans le temps.

### Liens utiles

[Postgresql Tuning](http://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server)

### Dépannage/troubleshoot

#### Voir les requêtes en cours d'exécution {#tuning-postgres-pg_stat_activity}

Pour voir les requêtes en cours d'exécution, Postgresql dispose de la table
[pg_stat_activity](http://www.postgresql.org/docs/9.1/static/monitoring-stats.html#MONITORING-STATS-VIEWS-TABLE) :

    [sql]
    postgres=# SELECT * FROM pg_stat_activity;
     datid  | datname  | procpid | usesysid | usename  | application_name | client_addr | client_port | backend_start                 | xact_start                    | query_start                   | waiting | current_query
    --------+----------+---------+----------+----------+------------------+-------------+-------------+-------------------------------+-------------------------------+-------------------------------+---------+---------------------------------
     11874  | postgres | 40516   |    10    | postgres | psql90           |             | -1          | 2011-01-17 11:49:52.022024+01 | 2011-01-17 11:53:43.766623+01 | 2011-01-17 11:53:43.766623+01 | f       | SELECT * FROM pg_stat_activity;
     207531 | 3.0.16   | 40570   | 16391    | freedom  |                  | ::1         | 57914       | 2011-01-17 11:50:28.902947+01 |                               | 2011-01-17 11:51:30.213533+01 | f       | VACUUM FULL ANALYSE;
    (2 rows)

Cette table présente le détail des requêtes en cours d'exécution :

current_query
:   La requête en cours d'exécution

procpid
:   le pid du process qui execute cette requête

backend_start
:   l'heure de reception de la requête

query_start
:   l'heure de début de traitement de la requête

waiting
:   le caractère « waiting » d'une requête si elle est bloqué ou en attente de libération d'une ressource par une autre requête.

La visualisation du champ *current_query* nécessite le paramètre *track_activities = on* (par défaut à *on*).
Si ce paramètre n'était pas actif, vous pouvez le définir dans `postgresql.conf` et recharger la conf à chaud :

Modification de la configuration :

    [bash]
    # vi /path/to/postgresql.conf
    …
    track_activities = on
    …

rechargement de la configuration :

    [sql]
    SELECT pg_reload_conf();
    SHOW track_activities;

##### Journaliser les requêtes longues

Lorsque l'observation de longues requêtes par [pg_stat_activity](#tuning-postgres-pg_stat_activity) n'est pas possible
(parce que le problème n'est pas reproductible ou observable à une heure précise de la journée par exemple),
on peut indiquer à Postgresql de logger les requêtes qui mettent plus N secondes à s'exécuter.
La durée est paramétrable via le paramètre [log_min_duration_statement](http://www.postgresql.org/docs/9.1/static/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT)
(par défaut '-1' : désactive le log). Vous pouvez soit le définir en global dans `postgresql.conf`, soit par base de données (via un ALTER DATABASE) :

Configuration globale commune à toutes les bases (ex. 5 minutes) :

    [bash]
    vi /path/to/postgresql.conf
    …
    log_min_duration = 5min
    …

rechargement de la configuration :

    [sql]
    select pg_reload_conf();


Paramétrage sur une base spécifique

    [sql]
    ALTER DATABASE dynacase SET log_min_duration_statement = '5min';


Les logs apparaissent alors dans `postgresql.log` sous la forme :

    2011-01-11 17:15:01 CET LOG: duration: 2.291 ms statement: SELECT * from paramv where type='G' or (type='A' and appid=1);

#### Connexion sécurisée (SSL)

* Configuration client : SSL Support (http://www.postgresql.org/docs/8.4/interactive/libpq-ssl.html)
* Configuration serveur : Secure TCP/IP Connections with SSL (http://www.postgresql.org/docs/8.4/interactive/ssl-tcp.html)

Utilisation de la directive 'sslmode=require' dans `pg_service.conf` pour forcer la connexion du client par SSL :

    [ini]
    [dynacase]
    host=pg.example.net
    port=5432
    user=dynacase
    password=secret
    dbname=dynacase
    sslmode=require


## Performances avec PHP

### Impact du mode SSL

L'utilisation du SSL entraine une surcharge lors de l'établissement d'une connection TCP sur PostgreSQL.

Exemple de surcharge pour l'initiation d'une connexion par TCP sur un serveur PostgreSQL en écoute sur l'interface locale de la machine (127.0.0.1) :

    .                           .--------------------------------.
                                | Temps pg_connect sur 127.0.0.1 |
    ,---------------------------+--------------------------------|
    | sslmode=disable           | + 0  ms                        |
    | sslmode=require (default) | + 15 ms                        |
    '------------------------------------------------------------'
    (Mesuré avec 1 x CPU Intel Core 2 Duo CPU T8300 @ 2.40GHz)

L'utilisation du SSL entraîne aussi une surcharge de traitement pour l'envoi du résultat au client.
Cette surcharge de traitement est proportionnelle au volume de données à envoyer et donc à chiffrer.

Exemple de surcharge pour le transfert des N premiers éléments d'une table conmposée d'une colonne de type *integer* (requête `SELECT * FROM foo LIMIT <N>`) :

    .             .------------------------.
                  | Temps requête avec SSL |
    ,-------------+------------------------|
    | LIMIT 10    | + 0,0551 ms            |
    | LIMIT 100   | + 0,0729 ms            |
    | LIMIT 1000  | + 0,401  ms            |
    | LIMIT 10000 | + 6,863  ms            |
    `--------------------------------------'
    (Mesuré avec 1 x CPU Intel Core 2 Duo CPU T8300 @ 2.40GHz)

Conclusion :

Si le réseau entre le serveur Dynacase et la base de données est « sûr », et si le niveau de sécurité souhaité le permet,
il peut être pertinent de désactiver le support SSL pour les connexions PostgreSQL.

### Connexions persistentes

Par défaut, Dynacase effectue une nouvelle connexion à la base de données pour chaque requête.

Le nombre de connexions initiés sur la base de données peut-être réduit via l'utilisation de connexions persistentes.

Le support du mode persistent, par l'extension PHP *pgsql*, est généralement actif par défaut et peut être modifié par le paramètre INI
[pgsql.allow_persistent](http://www.php.net/manual/en/pgsql.configuration.php) :

    [ini]
    pgsql.allow_persistent = 1

Une fois le support actif au niveau de PHP, il faut « re-configurer » Dynacase pour utiliser les connexions persistentes.

Pour activer les connexions persistentes sur Dynacase :

- Aller dans l'application "Administration" de Dynacase
- Onglet "paramètres applicatif" > "Platform Core"
- Positionner le paramètre "type de connexion" = "persistent"
- Exécuter `./wstart` dans le contexte Dynacase :

    [bash]
    ${WIFF_ROOT}/wiff context ${CONTEXT_NAME} exec ./wstart

Pour désactiver les connexions persistentes sur Dynacase :

- Positionner le paramètre "type de connexion" = "unpersistent"
- Exécuter `./wstart` dans le contexte Dynacase :

    [bash]
    ${WIFF_ROOT}/wiff context ${CONTEXT_NAME} exec ./wstart
