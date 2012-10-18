# Tuning Apache2

## MaxClients (et max_connections PostgreSQL)

La principale directive du mode MPM-Prefork de Apache est la directive [MaxClients](http://httpd.apache.org/docs/2.2/mod/mpm_common.html#maxclients) qui permet de configurer le nombre maximum de clients connectés et servis simultanément.

La valeur de ce paramètre dépend donc du nombre souhaité de clients servis en concurrent, mais aussi de la capacité du serveur : trop de processus Apache concurrents risquent d'entrainer une surcharge mémoire et forcer le système à utiliser son swap ce qui dégradera les temps de traitement.

L'utilisation mémoire depend de l'application qui est exécutée et doit donc être mesurée par l'exécution d'un scénario type par exemple.

Pour Journaliser l'utilisation mémoire de vos requêtes PHP vous pouvez insérer le log de la variable `%{mod_php_memory_usage}n` dans le *LogFormat* de votre fichier `access_log`.

Exemple :

    [apache]
    LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\" %{mod_php_memory_usage}n" combined

Important :

* La directive *MaxClients* doit être ajustée conjointement avec la directive *max_connections* de PostgreSQL. Dynacase effectuant généralement une connexion par requête HTTP, on doit donc avoir un *max_connections* PostgreSQL supérieur au *MaxClients* Apache sous peine de voir des requêtes mises en erreur.

Référence :

* [apache prefork](http://httpd.apache.org/docs/2.2/mod/prefork.html)

## KeepAlive

La directive [keepAlive](http://httpd.apache.org/docs/2.2/mod/core.html#keepalive) permet de réduire le nombre de connexions TCP initiés par les clients HTTP, et donc de réduire l'utilisation CPU de l'OS et du serveur Apache.

Le client pourra ainsi effectuer N requêtes (l'une à la suite de l'autre) sur cette même connexion TCP sans devoir faire N nouvelles connexions TCP pour chacune des requêtes.

Exemple :

    [apache]
    KeepAlive On

Référence :

* [apache keepalive](http://httpd.apache.org/docs/2.2/mod/core.html#keepalive)

## HostnameLookups et Logs

Outre le fait de servir des requêtes, le serveur Apache journalise des informations dans les fichiers `access.log` et `error.log`.

Lors de l'écriture d'entrées dans ces fichiers, le serveur Apache peut résoudre les adresses IP des clients connectés et inscrire leur nom DNS à la place de leur adresse IP.

Ces résolutions DNS prennent généralement du temps et il est donc recommandé de désactiver cette fonctionnalité :

    [apache]
    HostnameLookups Off

Le fait que ces fichiers `access.log` et `error.log` soient stockés par défaut localement sur le serveur Web peut entrainer une surcharge sur la machine : un nombre important de requêtes entrainera un nombre important d'opérations d'écritures de messages dans ces fichiers.

Il peut donc être bénéfique de déplacer ces fichiers de logs sur des disques distinct afin de minimiser les E/S sur le disque des fichiers servis par le serveur Apache. L'écriture de ces logs peut aussi être déportée sur un serveur tiers via le protocole *syslog*.

Référence :

* [apache hostname lookup](http://httpd.apache.org/docs/2.2/mod/core.html#hostnamelookups)
* [apache log](http://httpd.apache.org/docs/2.2/logs.html)
