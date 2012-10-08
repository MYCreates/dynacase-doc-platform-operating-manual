# Gestion des contextes


## Sélection d'un contexte

Lors de la sélection d'un contexte, les caractéristiques suivantes sont présentés.

## Informations

La section “Informations” présente les informations et configuration générale du contexte.
<table >
	<tr >
		<th > Champ </th><th > Description </th>
	</tr>
	<tr >
		<td > Name </td><td > Le nom du contexte Dynacase </td>
	</tr>
	<tr >
		<td > Root </td><td > Le chemin d&#039;accès au répertoire dans lequel sera installé le contexte. Le répertoire doit être accessible en lecture/écriture par Apache. Si le répertoire spécifié n&#039;existe pas, dynacase-control essayera de le créer. </td>
	</tr>
	<tr >
		<td > Description </td><td > Un champ permettant de décrire le contexte (optionnel). </td>
	</tr>
	<tr >
		<td > Url </td><td > Ce champ d&#039;information permet de saisir l'<acronym title="Uniform Resource Locator">URL</acronym> par laquelle sera accessible le contexte (optionnel). </td>
	</tr>
	<tr >
		<td > Registration </td><td > Si dynacase-control est enregistré avec votre compte EEC, vous pouvez enregistrer ce contexte avec votre compte EEC en cochant cette case. </td>
	</tr>
	<tr >
		<td > Repositories </td><td > Cocher la liste des dépôts de paquets que vous souhaitez utiliser pour installer ce contexte. Par défaut les dépôts de paquets configurés avec “Default” seront pré-sélectionnés. </td>
	</tr>
</table>
Le bouton `Modify Context` permet de modifier la description du contexte et la liste des dépôts utilisés par ce
dernier.

## Import Module

Cette interface permet d'importer et d'installer un module au format `.webinst` (Voir format webinst) dans le contexte.
Create Archive
Cette interface permet de Créer une archive du contexte au format `.fcz' (Voir format fcz) pour installer ce contexte sur une autre machine par exemple.

## Delete context

Ce bouton permet de supprimer le contexte et ses éléments associés (base de donnée, vault, etc. 

Installed
La section `Installed` présente la liste des modules `webinst` qui sont installés dans le contexte.
Lorsqu'un module a une mise-à-jour de disponible sur les dépôts de paquets, une icône  apparaît en début de ligne d'un module et la nouvelle version disponible est présentée dans la colonne "Available Version".
Pour mettre à jour le module, il faut alors cocher la ligne du module et cliquer sur le bouton `[Upgrade Selection]`.

Available
La section “Available” présente la liste des modules disponibles sur les dépôts de paquets.
Vous pouvez cocher les modules que vous souhaitez installer dans le contexte, et lancer l'installation en cliquant
sur le bouton `[Install Selection]`.


## Archivage et restauration de contexte

Archivage et restauration de contextes
dynacase-control permet de créer une archive d'un contexte qui contiendra tous les éléments du contexte en vue d'une restauration sur une autre machine par exemple.
### Archivage
La fonction d'archivage d'un contexte est accessible dans dynacase-control, sur la page du contexte dans la section "Information", bouton Create Archive.
L'archive inclut les éléments suivants :
Les fichiers du contexte.
Un dump de la base de données.
Les fichiers des vaults (optionnel : il est possible de désactiver l'archivage des fichiers du vault afin de réduire la taille de l'archive).
Les archives sont stockés dans le sous-répertoire `archived-contexts` de dynacase-control avec une extension `.fcz`.
### Restauration
Les archives `.fcz` présentes dans le sous-répertoire `archived-contexts` de dynacase-control sont présentés dans la section "Archives" de l'interface de dynacase-control, sous la liste "Context".

Pour restaurer une archive il faut cliquer sur le bouton `[Create Context]` et remplir les informations demandées :

  - Root : Le chemin d'accès du répertoire dans lequel les fichiers du contexte seront restaurés.
  - Core Database Service : Le nom du service PostgreSQL dans lequel sera restauré le dump de la base de données.
  - Vault Root : Le chemin d'accès du répertoire dans lequel les fichiers du vault seront restaurés.

Ces répertoires doivent être créés manuellement et être accessibles en écriture à l'utilisateur Apache.
### Sauvegarde et restauration de contextes
La sauvegarde se différencie de l'archivage par le fait que la sauvegarde est une opération effectuée par le service d'exploitation système, et qu'il peut s'intégrer plus facilement dans les procédures mises en place par le service informatique.
Ce chapitre va donc présenter les éléments à sauvegarder en se focalisant sur les aspects systèmes.
#### Éléments d'un contexte Dynacase
Un contexte Dynacase est composé des éléments suivants :

  - Le répertoire du contexte : il contient le code PHP des applications et des familles de documents.
  - La base de données : elle contient les documents et le paramétrage Dynacase.
  - Le ou les vaults : ils contiennent les fichiers insérés dans les documents Dynacase.
#### Sauvegarde

#### wstop
Afin d'assurer l'intégrité des données, l'accès au contexte sera bloqué afin que les utillisateurs ne puissent pas modifier les données durant le déroulement de la sauvegarde.
Pour cela il faudra lancer la commande `wstop' dans le contexte Dynacase qu'on sauvegarde :

    [bash]
     # /var/www/dynacase-control/wiff wstop <nomDuContexte>

