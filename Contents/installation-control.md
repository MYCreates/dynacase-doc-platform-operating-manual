# Installation de Dynacase Control {#manex-ref:ab61ed63-269b-458a-98ea-0accf470a460}

Dynacase Control est l'outil qui permet d'installer et de gérer des contextes Dynacase.

## Téléchargement {#manex-ref:f02a0bce-de60-4e0c-84a2-7ae2c3fcd52e}

Dynacase Control est téléchargeable ici : [http://dynacase.anakeen.com/control/dynacase-control-current.tar.gz](http://dynacase.anakeen.com/control/dynacase-control-current.tar.gz "Téléchargement de Dynacase Control").

_Vous pouvez vous reporter à l'annexe [Vérification de l'intégrité des éléments téléchargés](#controle-integrite) pour plus d'explication sur le contrôle de l'intégrité des données téléchargées._

## Installation {#manex-ref:a5697be7-1b68-4111-8673-4fa91e2687cf}

L'installation s'effectue sous le compte root.  
L'archive "tar.gz" récupérée doit être décompressée dans un répertoire servi par Apache, par exemple dans le DocumentRoot de la configuration Apache.

    [bash]
    root@server: cd /var/www      # Debian/Ubuntu  
    root@server: cd /var/www/html # RedHat/CentOS     

Télécharger [l'archive de Dynacase Control](http://dynacase.anakeen.com/control/dynacase-control-current.tar.gz "Téléchargement de Dynacase Control"):

    [bash]
    wget http://dynacase.anakeen.com/control/dynacase-control-current.tar.gz

Extraire l'archive et renommer le répertoire :

    [bash]
    tar zxf dynacase-control-current.tar.gz
    mv dynacase-control-*-* dynacase-control  

Modifier le propriétaire du répertoire de Dynacase Control pour être celui de l'utilisateur faisant tourner Apache.

    [bash]
    chown -R www-data: dynacase-control # Debian/Ubuntu
    chown -R apache: dynacase-control   # RedHat/Centos

## Première connexion {#manex-ref:555e344a-f5eb-49c0-8fb0-26262e5410a8}

L'url de connexion dépend de votre configuration Apache. Si Dynacase Control a été installé dans votre Document Root, l'URL de connexion est : [http://localhost/dynacase-control/](http://localhost/dynacase-control/ "Connexion à Dynacase Control")

Lors de la première connexion à Dynacase Control, vous devez saisir un login et un mot de passe afin de contrôler l'accès à l'interface de Dynacase Control. 

![ définition du login d'accès à Dynacase Control ](i1.png "définition du login d'accès à Dynacase Control")

Dynacase Control vous demande si vous souhaitez enregistrer ce dernier avec votre compte EEC.  
Cet enregistrement vous permet de déclarer l'installation de Dynacase conformément à votre contrat EEC.  

![ enregistrement EEC ](i2.png "enregistrement EEC")

Si vous n'avez pas de compte EEC, cliquez sur le bouton '[Register later…]'

Si vous n'enregistrez pas Dynacase Control lors de cette première connexion, vous aurez toujours la possibilité de le faire depuis l'interface Control.

## Dynacase Control {#manex-ref:9e721922-a423-474a-8f69-79beec1d27d6}

### Setup {#manex-ref:8f810563-02e8-4636-8dcf-d2b36ce50828}

![ interface Dynacase Control ](i3.png "interface Dynacase Control")


L'interface `Control > Setup` permet de configurer Dynacase Control. Vous pouvez, depuis cet écran:

* Mettre à jour Dynacase Control lorsqu'une nouvelle version est détectée
* Changer le mot de passe de connexion à Dynacase Control : “Dynacase Control Information > Password”
* Enregistrer son Dynacase Control avec son compte EEC : “Dynacase Control Information > Registration > Register”
* <span class="flag inline release from">control 1.5</span> (Dés)Activer le journal local des erreurs de Dynacase Control : “Local log > Local log On/Off”
* Ajouter/Supprimer/Modifier des dépôt de paquets : ”Repositories”
* Modifier les paramètres de Dynacase Control : ”Parameters”
* Voir les paramètres de PHP : “PHP Info”

![ interface setup Dynacase Control ](i4.png "interface setup de Dynacase Control")

#### Mise à jour de Dynacase Control {#manex-ref:4ac4d957-de1c-42ad-aa1a-9ada410308c5}

Lors de la connexion à l'interface Dynacase Control, celui-ci vérifie si une nouvelle version est disponible.
Si c'est le cas, alors une popup propose de télécharger et d'appliquer la mise à jour :

![mise à jour de Dynacase Control](i6.png "mise à jour de Dynacase Control")

Si vous refusez la mise à jour, il est alors possible d'appliquer ultérieurement la mise à jour en allant dans la section *Setup > Dynacase Control Information*, en cliquant sur le bouton *[Update]*.

##### Configuration des mises-à-jour de Dynacase Control {#installation-control-update}

Vous pouvez configurer l'hôte sur lequel Dynacase Control effectuera les recherches et téléchargements de mises-à-jour (c'est particulièrement utile lors de l'installation sur une machine n'ayant pas accès à internet).
Par défaut, la configuration pointe sur [le dépôt officiel Anakeen de Dynacase Control](http://dynacase.anakeen.com/control/).

| nom du paramètre     | valeur par défaut      | description                                                                                       |
| -                    | -                      | -                                                                                                 |
| wiff-update-host     | http://dynacase.anakeen.com | Le nom de l'hôte hébergeant les mises-à-jour avec le protocole à utiliser (“http://” ou “ftp://”) |
| wiff-update-path     | /control/         | Le chemin d'accès au répertoire des mises-à-jour                                                  |
| wiff-update-login    |                        | Login de connexion au serveur de mise à jour                                                      |
| wiff-update-password |                        | Mot de passe de connexion au serveur de mise à jour                                               |

#### Enregistrement de Dynacase Control {#manex-ref:9a7a2435-98a3-4e6c-b969-5c2cf61536fa}

L'enregistrement permet d'associer une instance de Dynacase Control avec votre contrat EEC.
Cet enregistrement va automatiquement envoyer sur les serveurs de Dynacase certaines informations de configuration de votre serveur, permettant de simplifier les éventuelles interventions ultérieures de support.
Enfin, l'enregistrement avec votre compte EEC va également ajouter automatiquement à Dynacase Control la liste des [dépôts de paquets](#installation-control-repositories) privés associés à votre contrat.

#### Dépôts de paquets {#installation-control-repositories}

La section *Repositories* permet d'ajouter, supprimer et éditer des dépôts de paquets qui pourront être utilisés pour créer un contexte Dynacase.

Lorsque vous souhaitez installer une version donnée de Dynacase, il vous faut ajouter de dépôt correspondant.

![ Édition de dépôt ](i5.png "édition de dépôt")

Lors de l'édition d'un dépôt, vous avez à renseigner les champs suivants:

| Nom du paramètre | description                                                                                                |
| -                | -                                                                                                          |
| Name             | Le nom du dépôt de paquets (caractère alpha-numérique uniquement).                                         |
| Description      | Un champ libre de description du dépôt (optionnel)                                                         |
| Protocol         | Protocole d'accès au dépôt : (*http*, *https*, *ftp*, *file*)                                              |
| Host             | Le nom d'hôte du serveur hébergeant le dépôt                                                               |
| Path             | Le chemin d'accès au répertoire _webinst_ du dépôt                                                         |
| Default          | Cocher pour que ce dépôt soit activé par défaut lors de la création d'un contexte Dynacase                 |
| Authenticated    | Cocher si le dépôt de paquets requiet une authentification (comme dans le cas d'un dépôt EEC par exemple). |
| Login            | Le nom d'utilisateur (compte EEC) pour l'authentification du dépôt                                         |
| Password         | Le mot de passe associé au login pour l'authentification du dépôt                                          |
| Confirm password | Confirmation du mot de passe                                                                               |

##### Dépôts pré-configurées {#manex-ref:f2b331d4-7389-44de-8ef5-f7cb31686966}
Par défaut, le [dépôt de la version communautaire de Dynacase Platform](https://dynacase.anakeen.com/stable/) est pré-configuré :
 
* Repository : Dynacase
* Protocol : https
* Host : dynacase.anakeen.com
* Path : stable/

## Utilisation d'un proxy {#manex-ref:35ec69be-4888-43d5-b796-5e7f0345415d}

Si vos accès aux dépôts Dynacase Platform nécessitent l'utilisation d'un proxy HTTP, vous pouvez définir celui-ci avec les paramètres suivants :

| Nom du paramètre | description                                                                              |
| -                | -                                                                                        |
| use-proxy        | Cocher se choix si vous souhaitez utiliser un proxy et compléter les paramètres suivants |
| proxy-host       | Le nom DNS (ou adresse IP) du proxy HTTP (Exemple : “proxy.example.net”)                 |
| proxy-port       | Le numéro de port du proxy HTTP (Exemple : “3128”)                                       |
| proxy-username   | Le nom d'utiliteur pour le proxy, s'il requiert une authentification                     |
| proxy-password   | le mot de passe associé si le proxy requiert une authentification                        |

## Délai de connexion {#manex-ref:d6ee7ebc-0711-49e2-b776-f4e9077683af}

Le paramètre `connect-timeout` (par défaut à `3`) permet de spécifier le temps
d'attente maximum pour l'établissement d'une connexion HTTP (e.g. connexion à
un dépôt de paquet pour vérifier son statut ou lister son contenu).

| Nom du paramètre | description |
| - | - |
| connect-timeout  | Temps d'attente maximum (en secondes) pour l'établissement d'une connexion HTTP |

* Si la valeur est positionnée à `0` (zéro), alors le temps d'attente maximum
  sera le temps d'attente maximum par défaut de la librairie cURL (soit `300`
  secondes).
* Si vous avez déclaré de nombreux dépôts de paquets inaccessibles, cela peut
  entrainer des ralentissements et des blocages transitoires dans l'interface.
  Dans ces cas là, il est préférable de supprimer les dépôts inaccessibles et
  éviter ainsi de devoir attendre l'expiration du délais de connexion à chaque
  accès à ces dépôts par dynacase-control.

## Journal des messages d'erreurs {#manex-ref:022e6b2f-cbe7-4ead-8f84-3ed8d0d718c9}

<span class="flag inline release from">control 1.5</span>

Les messages d'erreurs émis par dynacase-control sont enregistrés de la manière
suivante :

* Le message est systématiquement envoyé au gestionnaire syslog local avec le
tag `dynacase-control` et la "facility" déclarée par le paramètre
[`syslog-facility`][syslog-facility] (`LOG_USER` par défaut).

* Si le paramètre `Local log > Local log On/Off` est activé (dans la section
[`Setup`][setup]), le message est aussi envoyé dans le fichier `log/wiff.log` et
consultable avec le bouton `Local log > View` de la section [`Setup`][setup].

### Paramétrage syslog {#manex-ref:cedce829-1eb2-4610-bb0f-c0f8ad17cbeb}

#### `syslog-facility` {#manex-ref:95dce35e-55b5-4b57-8087-d743dc6f022d}

La "facility" des messages émis par syslog est paramétrable par le paramètre
`syslog-facility` qui peut prendre les valeurs suivantes :

`LOG_AUTH`, `LOG_AUTHPRIV`, `LOG_CRON`, `LOG_DAEMON`, `LOG_KERN`, `LOG_LOCAL0`,
`LOG_LOCAL1`, `LOG_LOCAL2`, `LOG_LOCAL3`, `LOG_LOCAL4`, `LOG_LOCAL5`,
`LOG_LOCAL6`, `LOG_LOCAL7`, `LOG_LPR`, `LOG_MAIL`, `LOG_NEWS`, `LOG_SYSLOG`,
`LOG_USER`, `LOG_UUCP`.

La valeur du paramètre n'est pas modifiable par l'interface Web Pour modifier sa
valeur il faut éditer le fichier `conf/params.xml` et changer la propriété
`value` du paramètre `syslog-facility`.

Exemple pour logger avec la "facility" `LOG_LOCAL0` :

    [xml]
    <param name="syslog-facility" mode="hidden" value="LOG_LOCAL0" />

Si la valeur de ` syslog-facility` est vide, alors la "facility" `LOG_USER` est
utilisée.

Si la valeur de `syslog-facility` n'est pas une "facility" valide, alors la
"facility" `LOG_USER` est utilisée et un message d'erreur est émis indiquant que
la "facility" spécifiée est invalide (`Invalid syslog-facility 'XXX'. Using
default facility.`).

<!-- links -->
[setup]: #manex-ref:8f810563-02e8-4636-8dcf-d2b36ce50828
[php:error_log]: http://www.php.net/manual/errorfunc.configuration.php#ini.error-log
[syslog-facility]: #manex-ref:95dce35e-55b5-4b57-8087-d743dc6f022d
