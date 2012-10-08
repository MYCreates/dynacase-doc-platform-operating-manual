# Installation de Dynacase Control


Dynacase Control est l'outil qui permet d'installer et de gérer des contextes Dynacase.

## Téléchargement

Dynacase Control est téléchargeable ici : [http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz](http://eec.anakeen.com/public/control/dynacase-control-current.tar.gz "Téléchargement de Dynacase Control").

## Installation

L'installation s'effectue sous le compte `root`.  
L'archive "tar.gz" récupérée doit être décompressée dans un répertoire servi par Apache, par exemple dans le `DocumentRoot` de la configuration Apache.

    [bash]
    root@server: cd /var/www      # Debian/Ubuntu  
    root@server: cd /var/www/html # RedHat/CentOS     

Télécharger l'archive de Dynacase Control :  

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

L'url de connexion dépend de votre configuration Apache. Si Dynacase Controla été installé dans votre `Document Root`, l'URL de connexion est : [http://localhost/dynacase-control/](http://localhost/dynacase-control/ "Connexion à Dynacase Control")

Lors de la première connexion à Dynacase Control, vous devez saisir un login et un mot de passe afin de contrôler l'accès à l'interface de Dynacase Control. 

![définition du login d'accès à Dynacase Control](i1.png "définition du login d'accès à Dynacase Control")

Dynacase Control vous demande si vous souhaitez enregistrer ce dernier avec votre compte EEC.  
Cet enregistrement vous permet de déclarer l'installation de Dynacase conformément à votre contrat EEC.  

![enregistrement EEC](i2.png "enregistrement EEC")

Si vous n'avez pas de compte EEC, cliquez sur le bouton '[Register later…]'

Si vous n'enregistrez pas Dynacase Control lors de cette première connexion, vous aurez toujours la possibilité de le faire depuis l'interface Control.

## Dynacase Control


### Setup

![interface Dynacase Control](i3.png "interface Dynacase Control")


L'interface `Control > Setup` permet de configurer Dynacase Control.

* Mettre à jour Dynacase Control lorsqu'une nouvelle version est détectée
* Changer le mot de passe de connexion à Dynacase Control : “Dynacase Control Information > Password”
* Enregistrer son Dynacase Control avec son compte EEC : “Dynacase Control Information > Registration > Register”
* (Dé)Activer le mode debug de Dynacase Control : “Debug > Debug Mode On/Off”
* Ajouter/Supprimer/Modifier des dépôt de paquets : ”Repositories”
* Modifier les paramètres de Dynacase Control : ”Parameters”
* Voir les paramètres de PHP : “PHP Info”

![interface setup Dynacase Control](i4.png "interface setup Dynacase Control")

### Dépôts de paquets

La section “Repositories” permet d'ajouter, supprimer et éditer des dépôts de paquets qui pourront être utilisés pour créer un contexte Dynacase.

**Vous devez déclarer ici les dépôts que vous souhaitez utiliser.
Si vous avez un compte EEC et que Dynacase Control est enregistré, vos dépôts privés vous seront automatiquement proposés.**

![édition de dépôt](i5.png "édition de dépôt")

<table>
    <tr>
        <td>Libellé</td>
	<td>Valeur</td>
    </tr>
    <tr>
        <td>Name</td>
        <td>Le nom du dépôt de paquets (caractère alpha-numérique uniquement).</td>
    </tr>
    <tr>
        <td>Description</td>
        <td>Un champ libre de description du dépôt (optionnel)</td>
    </tr>
    <tr>
        <td>Protocol</td>
        <td>Protocole d'accès au dépôt : http/https/ftp/file</td>
    </tr>
    <tr>
        <td>Host</td>
        <td>Le nom d'hôte du serveur hébergeant le dépôt</td>
    </tr>
    <tr>
        <td>Path</td>
        <td>Le chemin d'accès au répertoire _webinst_ du dépôt</td>
    </tr>
    <tr>
        <td>Default</td>
        <td>Cocher pour que ce dépôt soit activé par défaut lors de la création d'un contexte Dynacase</td>
    </tr>
    <tr>
        <td>Authenticated</td>
        <td>Cocher si le dépôt de paquets requiet une authentification (comme dans le cas d'un dépôt EEC par exemple).</td>
    </tr>
    <tr>
        <td>Login</td>
        <td>Le nom d'utilisateur (compte EEC) pour l'authentification du dépôt</td>
    </tr>
    <tr>
        <td>Password</td>
        <td>Le mot de passe associé au login pour l'authentification du dépôt</td>
    </tr>
    <tr>
        <td>Confirm password</td>
        <td>Confirmation du mot de passe</td>
    </tr>
</table>
 

### Dépôts pré-configurées
Par défaut, les dépôts de la version communautaire de Dynacase sont pré-configurées : 
 
*   Repository : Dynacase  
*   Protocol : https  
*   Host : eec.anakeen.com  
*   Path public/platform/  


## Mise à jour de Dynacase Control


Lors de la connexion à l'interface Dynacase Control, celui-ci vérifie si une nouvelle version est disponible.  
Si c'est le cas, alors une popup propose de télécharger et d'appliquer la mise à jour :  

![mise à jour de Dynacase Control](i6.png "mise à jour de Dynacase Control")

Si vous refusez la mise à jour, il est alors possible d'appliquer ultérieurement la mise à jour en allant dans la section `Setup > Dynacase Control Information`, en cliquant sur le bouton `[Update]`.

### Configuration des mises-à-jour de Dynacase Control
Lors du lancement de l'interface de Dynacase Control, celui-ci va vérifier si une mise-à-jour est disponible.
Vous pouvez configurer avec ces paramètres l'hôte sur lequel Dynacase Control effectuera les recherches et téléchargements de mises-à-jour.
Par défaut, la configuration pointe sur le dépôt officiel Anakeen de Dynacase Control.

<table>
	<tr>
		<td>wiff-update-host</td>
		<td>http://eec.anakeen.com</td>
		<td>Le nom de l'hôte hébergeant les mises-à-jour avec le protocole à utiliser (“http:” ou “ftp:”) </td>
	</tr>
	<tr>
		<td>wiff-update-path</td>
		<td>public/control</td>
		<td>Le chemin d'accès au répertoire des mises-à-jour</td>
	</tr>
	<tr>
		<td>wiff-update-login</td>
		<td></td>
		<td>Login de connexion au serveur de mise à jour</td>
	</tr>
	<tr>
		<td>wiff-update-password</td>
		<td></td>
		<td>Mot de passe de connexion au serveur de mise à jour</td>
	</tr>
</table>


## Utilisation d'un proxy

Si vos accès aux dépôts Dynacase nécessitent l'utilisation d'un proxy HTTP, vous pouvez définir celui-ci avec les
paramètres :
 
<table>
	<tr>
		<td>use-proxy</td>
		<td>Cocher se choix si vous souhaitez utiliser un proxy et compléter les paramètres suivants</td>
	</tr>
	<tr>
		<td>proxy-host</td>
		<td>Le nom DNS (ou adresse IP) du proxy HTTP (Exemple : “proxy.example.net”)</td>
	</tr>
	<tr>
		<td>proxy-port</td>
		<td>Le numéro de port du proxy HTTP (Exemple : “3128”)</td>
	</tr>
	<tr>
		<td>proxy-username</td>
		<td>Le nom d'utiliteur pour le proxy, s'il requiert une authentification</td>
	</tr>
	<tr>
		<td>proxy-password</td>
		<td>e mot de passe associé si le proxy requiert une authentification</td>
	</tr>
</table>


