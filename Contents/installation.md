# Installation d'un contexte

L'installation de Dynacase Platform et de ses modules est réalisée en créant un *contexte* Dynacase Control.

Un contexte regroupe l'ensemble des éléments nécessaires au fonctionnement d'une application Dynacase Platform : code des différents modules (installés ou générés), données et paramètres de configuration.

## Création d'un contexte

L'item *Create Context* de l'interface Dynacase Control ouvre la fenêtre de création d'un nouveau contexte.

![création d'un contexte](i7.png "création d'un contexte")

Vous êtes invité à saisir les paramètres du contexte :

| Nom du paramètre | description                                                                                                                                                                                                                         |
| -                | -                                                                                                                                                                                                                                   |
| Name             | Le nom du contexte                                                                                                                                                                                                                  |
| Root             | Chemin racine du répertoire d'installation du contexte. Il doit être accessible en lecture/écriture par Apache. Si le répertoire spécifié n'existe pas, Dynacase Control essayera de le créer.                                      |
| Description      | Description du contexte (optionnel)                                                                                                                                                                                                 |
| Url              | URL d'accès à Dynacase Platform, dépend de la configuration de votre serveur Apache (optionnel)                                                                                                                                     |
| Registration     | Si Dynacase Control est enregistré avec votre compte EEC, vous pouvez enregistrer ce contexte avec votre compte EEC en cochant cette case.                                                                                          |
| Repositories     | Cocher la liste des dépôts de paquets que vous souhaitez utiliser pour installer ce contexte. Par défaut les dépôts de paquets configurés (dans l'interface Control > Setup > Repositories) avec “Default” seront pré-sélectionnés. |
 
Lorsque le contexte est créé, celui-ci apparaît dans la rubrique *Context* de l'interface de Dynacase Control.

_Vous pouvez vous reporter à l'annexe [Vérification de l'intégrité des éléments téléchargés](#controle-integrite) pour plus d'explication sur le contrôle de l'intégrité des données téléchargées._

## Sélection des modules

En sélectionnant le contexte créé, son détail et la liste des modules disponibles sont affichés.

![détail d'un contexte](i8.png "détail d'un contexte")

Vous pouvez maintenant sélectionner les modules que vous souhaitez installer parmi ceux proposés à la rubrique `Available`.

Les modules pré-sélectionnés (comme Dynacase Core) sont obligatoires.

En cliquant sur le bouton *[Install Selection]* , le processus d'installation commence.
Dynacase Control vérifie les dépendances des paquets sélectionnés, et propose la liste des paquets qui seront installés.

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

Le panneau principal détaille l'exécution des processus de pré-installation, installation et post-installation.

La liste des modules à installer est présentée dans la liste “Module List”.
Les modules installés avec succès sont marqués par une pastille verte.

Les téléchargements des modules sont lancés.

### Licence

Si nécessaire, il vous est demandé d'accepter les contrats de licence affichés.

![contrat de licence](i10.png "contrat de licence")

### Paramètres

Les modules peuvent nécessiter des paramètres pour leur installation ou leur fonctionnement.

![paramètres du module](i11.png "paramètres du module")

L'installation de Dynacase Core (exemple ci-dessus) requiert les paramètres suivants : 

| Nom du paramètre | Description |
| - | - |
| client name | information affichée sur la page d'authentification Dynacase |
| database postgresql service name | le nom du service postgresql pour accéder à la base dédiée pour ce contexte |
| authenticate default mode | mode d'authentification par défaut : “html” pour l'authentification par une page HTML et “basic” pour une authentification par le mécanisme HTTP Basic |
| output representation of date in database | le format de représentation des dates en base de données |
| temporary folder | chemin du répertoire où seront stockés les fichiers temporaires |
| admin password | mot de passe du compte super administrateur `admin` |
| enable compression | compression des données avec le module Apache `mod_deflate` |

### Fin de l'installation
 
À la fin de l'installation, les modules installés sont présentés dans la section “Installed”.

![fin de l'installation](i13.png "fin de l'installation")
