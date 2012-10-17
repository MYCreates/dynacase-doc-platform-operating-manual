# Installation de Dynacase Control

Dynacase Control est l'outil qui permet d'installer et de gérer des contextes Dynacase.

## Téléchargement

Dynacase Control est téléchargeable ici : [http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz](http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz "Téléchargement de Dynacase Control").

## Installation

L'installation s'effectue sous le compte root.  
L'archive "tar.gz" récupérée doit être décompressée dans un répertoire servi par Apache, par exemple dans le DocumentRoot de la configuration Apache.

    [bash]
    root@server: cd /var/www      # Debian/Ubuntu  
    root@server: cd /var/www/html # RedHat/CentOS     

Télécharger [l'archive de Dynacase Control](http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz "Téléchargement de Dynacase Control"):

    [bash]
    wget http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz

Extraire l'archive et renommer le répertoire :

    [bash]
    tar zxf dynacase-control-current.tar.gz
    mv dynacase-control-*-* dynacase-control  

Modifier le propriétaire du répertoire de Dynacase Control pour être celui de l'utilisateur faisant tourner Apache.

    [bash]
    chown -R www-data: dynacase-control # Debian/Ubuntu
    chown -R apache: dynacase-control   # RedHat/Centos

## Première connexion

L'url de connexion dépend de votre configuration Apache. Si Dynacase Control a été installé dans votre Document Root, l'URL de connexion est : [http://localhost/dynacase-control/](http://localhost/dynacase-control/ "Connexion à Dynacase Control")

Lors de la première connexion à Dynacase Control, vous devez saisir un login et un mot de passe afin de contrôler l'accès à l'interface de Dynacase Control. 

![ définition du login d'accès à Dynacase Control ](i1.png "définition du login d'accès à Dynacase Control")

Dynacase Control vous demande si vous souhaitez enregistrer ce dernier avec votre compte EEC.  
Cet enregistrement vous permet de déclarer l'installation de Dynacase conformément à votre contrat EEC.  

![ enregistrement EEC ](i2.png "enregistrement EEC")

Si vous n'avez pas de compte EEC, cliquez sur le bouton '[Register later…]'

Si vous n'enregistrez pas Dynacase Control lors de cette première connexion, vous aurez toujours la possibilité de le faire depuis l'interface Control.

## Dynacase Control

### Setup

![ interface Dynacase Control ](i3.png "interface Dynacase Control")


L'interface `Control > Setup` permet de configurer Dynacase Control. Vous pouvez, depuis cet écran:

* Mettre à jour Dynacase Control lorsqu'une nouvelle version est détectée
* Changer le mot de passe de connexion à Dynacase Control : “Dynacase Control Information > Password”
* Enregistrer son Dynacase Control avec son compte EEC : “Dynacase Control Information > Registration > Register”
* (Dés)Activer le mode debug de Dynacase Control : “Debug > Debug Mode On/Off”
* Ajouter/Supprimer/Modifier des dépôt de paquets : ”Repositories”
* Modifier les paramètres de Dynacase Control : ”Parameters”
* Voir les paramètres de PHP : “PHP Info”

![ interface setup Dynacase Control ](i4.png "interface setup de Dynacase Control")

#### Mise à jour de Dynacase Control

Lors de la connexion à l'interface Dynacase Control, celui-ci vérifie si une nouvelle version est disponible.
Si c'est le cas, alors une popup propose de télécharger et d'appliquer la mise à jour :

![mise à jour de Dynacase Control](i6.png "mise à jour de Dynacase Control")

Si vous refusez la mise à jour, il est alors possible d'appliquer ultérieurement la mise à jour en allant dans la section *Setup > Dynacase Control Information*, en cliquant sur le bouton *[Update]*.

##### Configuration des mises-à-jour de Dynacase Control {#installation-control-update}

Vous pouvez configurer l'hôte sur lequel Dynacase Control effectuera les recherches et téléchargements de mises-à-jour (c'est particulièrement utile lors de l'installation sur une machine n'ayant pas accès à internet).
Par défaut, la configuration pointe sur [le dépôt officiel Anakeen de Dynacase Control](http://eec.anakeen.com/public/control).

| nom du paramètre     | valeur par défaut      | description                                                                                       |
| -                    | -                      | -                                                                                                 |
| wiff-update-host     | http://eec.anakeen.com | Le nom de l'hôte hébergeant les mises-à-jour avec le protocole à utiliser (“http://” ou “ftp://”) |
| wiff-update-path     | public/control         | Le chemin d'accès au répertoire des mises-à-jour                                                  |
| wiff-update-login    |                        | Login de connexion au serveur de mise à jour                                                      |
| wiff-update-password |                        | Mot de passe de connexion au serveur de mise à jour                                               |

#### Enregistrement de Dynacase Control

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

##### Dépôts pré-configurées
Par défaut, les [dépôts de la version communautaire de Dynacase Platform](https://eec.anakeen.com/public/platform/) sont pré-configurées :
 
* Repository : Dynacase
* Protocol : https
* Host : eec.anakeen.com
* Path : public/platform/

## Utilisation d'un proxy

Si vos accès aux dépôts Dynacase Platform nécessitent l'utilisation d'un proxy HTTP, vous pouvez définir celui-ci avec les paramètres suivants :

| Nom du paramètre | description                                                                              |
| -                | -                                                                                        |
| use-proxy        | Cocher se choix si vous souhaitez utiliser un proxy et compléter les paramètres suivants |
| proxy-host       | Le nom DNS (ou adresse IP) du proxy HTTP (Exemple : “proxy.example.net”)                 |
| proxy-port       | Le numéro de port du proxy HTTP (Exemple : “3128”)                                       |
| proxy-username   | Le nom d'utiliteur pour le proxy, s'il requiert une authentification                     |
| proxy-password   | le mot de passe associé si le proxy requiert une authentification                        |

