# Tuning PostgreSQL {#manex-ref:4de10551-45b5-4796-964a-55b3187b5942}

Dans le cadre de son utilisation avec Dynacase, une couche d’abstraction logicielle Dynacase sur PostgreSQL permet d’optimiser son utilisation. Les éventuels tuning de la base PostgreSQL dépendent entièrement du modèle documentaire mis en œuvre par Dynacase. Ils sont réalisés ou préconisés si nécessaire lors de la mise en œuvre de la solution par un expert Anakeen.

## Configuration de PostgreSQL {#manex-ref:8e61f997-bf97-46e6-85b8-0fb78d067a42}

La plupart des distributions livrent PostgreSQL avec une configuration basique, passe-partout, qui n'est pas forcement adaptée au volume et au nombre de transactions que peut générer Dynacase.
Voici quelques éléments de configuration pour une machine type avec 2 Go à 3 Go de RAM.

### shared_buffers {#manex-ref:0359ad83-2de3-4486-b9e1-cc57fcc50370}

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

### effective_cache_size {#manex-ref:8ddc52ea-caae-417b-bb36-7a36ba8f6ff8}

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

### work_mem {#manex-ref:cfed533d-ebf4-4c0f-b543-04236cad07bb}

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

### maintenance_work_mem {#manex-ref:a2d8efda-00ff-4bd9-a1cb-864d98bb138a}

Le paramètre [maintenance_work_mem](http://www.postgresql.org/docs/9.1/static/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) spécifie la quantité de mémoire que peuvent utiliser les commandes/process PostgreSQL de VACUUM, création d'index et altération de table. Une valeur plus grande lui permettra de pouvoir manipuler plus de données en mémoire vive, et accélèrera le temps de traitement sur les VACUUM (freedom_clean) par exemple.

    [bash]
    vi /var/lib/pgsql/data/postgresql.conf
    […]
    maintenance_work_mem = 128Mb
    […]

### WAL : Write Ahead Log {#manex-ref:627c1e75-d746-44b3-bfac-607adc70c66a}

Le mécanisme de WAL (Write Ahead Log) est le mécanisme utilisé par Postgresql pour gérer les modifications des données de la base et assurer l'intégrité de la base de données en cas de crash du serveur.

Lorsqu'une donnée est modifiée, l'opération est d'abord enregistrée dans un fichier WAL sur le disque. Ensuite, de manière asynchrone, les opérations contenues dans les fichiers WAL sont écrites de manière définitive sur la base de données.

Suite à un crash, au redémarrage, le serveur Postgresql rejouera alors les opérations des fichiers WAL qui ne sont pas encore inscrites en base pour mettre à niveau la base de données.

#### wal_buffers {#manex-ref:2d5949b7-5fe9-4f97-8aa7-48c64c340447}

Le paramètre [wal_buffers](http://www.postgresql.org/docs/9.1/static/runtime-config-wal.html#GUC-WAL-BUFFERS) spécifie la taille d'un fichier de journal d'écriture "WAL" (Write-Ahead-Log).

Par défaut, la taille d'un WAL est de `64KB`. Une petite valeur entraînera des écritures de petits fichiers WAL plus fréquentes, et une grande valeur entraînera des écritures de fichiers WAL de plus gros volume mais avec une fréquence moindre.

Il est admis qu'une valeur de `16MB` est une bonne valeur pour un serveur de production :

	wal_buffers = 16MB

### checkpoint_segments, checkpoint_timeout et checkpoint_completion_target {#manex-ref:1ccab0b1-2e5e-4c73-8d68-0b8578893752}

Le paramètre [checkpoint_segments](http://www.postgresql.org/docs/9.1/static/runtime-config-wal.html#GUC-CHECKPOINT-SEGMENTS) spécifie le nombre de fichiers WAL qui sont conservés, en rotation, sur le disque.

Une grand nombre de `checkpoint_segments` réduit le nombre d'écritures sur la base de données, mais en cas de crash du serveur, cela fera un plus gros volume de données à rejouer lors du redémarrage du serveur pour qu'il remette sa base à niveau avec les derniers fichiers checkpoints.

Le paramètre [checkpoint_timeout](http://www.postgresql.org/docs/9.1/static/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) spécifie le temps maximal qu'un fichier WAL peut rester sur le disque avant d'être inscrit de manière définitive dans la base de données.

Le paramètre [checkpoint_completion_target](http://www.postgresql.org/docs/9.1/static/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) permet quand à lui de spécifier la périodicité d'écriture des fichiers WAL sur la base de données. Il permet de répartir les écritures des fichiers WAL au cours du temps et d'éviter d'avoir des pics d'écritures lorsque les fichiers WAL atteignent leur limite de temps au même moment.

Une estimation du nombre de fichiers WAL stockés sur le disque est donné par la formule suivante :

	number of WAL files = ( 2 + checkpoint_completion_target ) * checkpoint_segments + 1

Les valeurs suivantes sont admises comme étant un bon compromis entre la réduction du nombre d'écritures sur la base et le temps de reprise en cas de crash.

	checkpoint_segments = 32
	checkpoint_timeout = 5min
	checkpoint_completion_target = 0.9

Le volume occupé par les fichiers WAL sera alors approximativement de 1.5GB.

### Bulk data loading {#752E8DED-D951-42E9-BF23-73AD28622D03}

Dans le cas de la restauration d'une base de donnée, ou du chargement massif de données, ces paramètres de WAL peuvent être augmentés afin de réduire le nombre d'opérations d'écritures sur la base de donnée, moyennant une utilisation d'un plus grand volume par les fichiers WAL.

	checkpoint_segments = 300
	checkpoint_timeout = 3000
	autovacuum = off

Attention : Ces paramètres ne doivent pas être utilisés en production, mais seulement temporairement pour la durée de l'opération de chargement/restauration des données.

### Conclusion {#manex-ref:40f882d6-2cb6-4664-9e8e-035978d200fc}

Ces paramètres permettent donc de donner une plus grande liberté de mouvement à PostgreSQL comparés à ceux livrés par défaut.
Les valeurs données ci-dessus sont des exemples qu'il faudra bien sûr adapter/moduler en fonction de la machine et de son utilisation dans le temps.

### Liens utiles {#manex-ref:0c7ec39e-7da7-48ed-bb2f-490d69aa4077}

[Postgresql Tuning](http://wiki.postgresql.org/wiki/Tuning_Your_PostgreSQL_Server)

### Dépannage/troubleshoot {#manex-ref:7b74a4e0-6dff-42c4-834e-4b07765d9aec}

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

##### Journaliser les requêtes longues {#manex-ref:2b296094-4059-417f-8d7a-c6af72173a4e}

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

#### Connexion sécurisée (SSL) {#manex-ref:e1753887-1dbf-4d1c-a534-d37f333cc39b}

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


## Performances avec PHP {#manex-ref:fb8bcc29-58b7-403a-bd88-7a4e1ff58e20}

### Impact du mode SSL {#manex-ref:18ed0b78-f22a-4854-accd-c1ec5c7b650d}

L'utilisation du SSL entraîne une surcharge lors de l'établissement d'une connection TCP sur PostgreSQL.

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
il peut être avantageux en terme de performance de désactiver le support SSL.

