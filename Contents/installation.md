# Installation


L'installation de Dynacase Platform et ses modules est réalisée en créant un *contexte* Dynacase Control.

Un contexte regroupe l'ensemble des éléments nécessaires au fonctionnement d'une application Dynacase : code des différents modules (installés ou générés), données et paramètres de configuration.

Pour plus de précision sur les contextes et leur gestion reportez-vous au chapitre *Contextes Dynacase*.

## Création d'un contexte


L'item `Create Context` de l'interface Dynacase Control ouvre la fenêtre de création d'un nouveau contexte.

![création d'un contexte](i7.png "création d'un contexte")

Vous êtes invité à saisir les paramètres du contexte.
<table>
	<tr>
		<td>Name</td>
		<td>Le nom du contexte</td>
	</tr>
	<tr>
		<td>Root</td>
		<td>Chemin racine du répertoire d'installation du contexte. Il doit être accessible en lecture/écriture par Apache. Si le répertoire spécifié n'existe pas, Dynacase Control essayera de le créer.</td>
	</tr>
	<tr>
		<td>Description</td>
		<td>Description du contexte (optionnel)</td>
	</tr>
	<tr>
		<td>Url</td>
		<td>URL d'accès à Dynacase Platform, dépend de la configuration de votre serveur Apache (optionnel)</td>
	</tr>
	<tr>
		<td>Registration</td>
		<td>Si Dynacase Control est enregistré avec votre compte EEC, vous pouvez enregistrer ce contexte avec votre compte EEC en cochant cette case.</td>
	</tr>
	<tr>
		<td>Repositories</td>
		<td>Cocher la liste des dépôts de paquets que vous souhaitez utiliser pour installer ce contexte. Par défaut les dépôts de paquets configurés (dans l'interface Control > Setup > Repositories) avec “Default” seront pré-sélectionnés. </td>
	</tr>
</table>

 
Lorsque le contexte est créé, celui-ci apparaît dans la rubrique `Context` de l'interface de Dynacase Control.

## Sélection des modules

En sélectionnant le contexte créé, son détail et la liste des modules disponibles sont affichés.

![détail d'un contexte](i8.png "détail d'un contexte")

Vous pouvez maintenant sélectionner les modules que vous souhaitez installer parmi ceux proposés à la rubrique `Available`.

Les modules pré-sélectionnés (comme Dynacase Platform) sont obligatoires.

En cliquant sur le bouton `[Install Selection]` , le processus d'installation commence.
Dynacase Control vérifie les dépendances des paquets sélectionnés, et propose éventuellement la liste des paquets à
installer.

![contrôle des dépendances](i9.png "contrôle des dépendances")

Si vous êtes d'accord avec la liste proposée, vous pouvez valider cette liste et poursuivre l'installation.

## Processus d'installation

Chacun des modules sélectionnés pour l'installation va suivre ce même processus d'installation :

* demande de licence
* demande des paramètres du module
* exécution des processus de pre-installation
* exécution des processus d'installation
* exécution des processus de post-installation

![process d'installation](i12.png "process d'installation")

Les téléchargements des modules sont lancés.

Si nécessaire, il vous est demandé d'accepter les contrats de licence affichés.

![contrat de licence](i10.png "contrat de licence")

## Paramètres

Les modules peuvent nécessiter des paramêtres pour leur installation ou leur fonctionnement.

![paramètres du module](i11.png "paramètres du module")

L'installation de Dynacase Platform (exemple ci-dessus) requiert les paramêtres suivants : 

<table>
	<tr>
		<td>client name</td>
		<td>information affichée sur la page d'authentification Dynacase</td>
	</tr>
	<tr>
		<td>database postgresql service name</td>
		<td>le nom du service postgresql pour accéder à la base dédiée pour ce contexte</td>
	</tr>
	<tr>
		<td>authenticate default mode</td>
		<td>mode d'authentification par défaut : “html” pour l'authentification par une page HTML et “basic” pour une authentification par le mécanisme HTTP Basic</td>
	</tr>
	<tr>
		<td>apache system user</td>
		<td>utilisateur sous lequel est exécuté Apache (“www-data” pour Debian/Ubuntu, “apache” pour RedHat/CentOS)</td>
	</tr>
	<tr>
		<td>temporary folder</td>
		<td>chemin du répertoire où seront stockés les fichiers temporaires</td>
	</tr>
	<tr>
		<td>admin password</td>
		<td>mot de passe du compte super administrateur `admin` </td>
	</tr>
	<tr>
		<td>enable compression</td>
		<td>compression des données avec le module Apache mod_deflate</td>
	</tr>
</table>

 

 
La liste des modules à installer est présentée dans la liste “Module List”.
Les modules installés avec succès sont marqués par une pastille verte.
Le panneau principal détaille l'exécution des processus de pre-installation, installation et post-installation.

## Fin de l'installation

À la fin de l'installation, les modules installés sont présentés dans la section “Installed”.

![fin de l'installation](i13.png "fin de l'installation")

