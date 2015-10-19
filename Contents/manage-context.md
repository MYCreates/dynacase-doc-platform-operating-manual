*[URL]: Uniform Resource Locator

# Gestion des contextes {#manex-ref:1ea676c8-8a00-4466-bd59-a86df03a32c4}

## Description d'un contexte {#manex-ref:5ce98410-a6c0-4b88-8884-a66deb1d3e0a}

Lors de la sélection d'un contexte, les sections suivantes sont présentées :

Informations
:   Les caractéristiques du contexte

Installed
:   la liste des modules installés

Available
:   la liste des modules disponibles

### Informations {#manex-ref:11bd31cd-3ded-4c3c-a393-caf9073ff980}

La section “Informations” présente les informations et la configuration générale du contexte.

| Champ        | Description                                                                                                                                                                                                              |
| -            | -                                                                                                                                                                                                                        |
| Root         | le chemin d'accès au répertoire dans lequel est installé le contexte.         |
| Description  | description du contexte.                                                      |
| Url          | URL d'accès                                                                   |
| Registration | vous pouvez consulter les informations enregistrées. Si Dynacase Control est déclaré avec votre compte EEC, vous pouvez demander l'enregistrement de ce contexte.                                                                                |
| Repositories | liste les dépôts utilisée pour installer ou mettre à jour les modules                                        |

de plus, les actions suivates sont disponibles au travers des boutons :

Modify Context
:   Permet de modifier la description du contexte et la liste des dépôts utilisés par ce dernier.

Import Module
:   Permet d'installer un module format *.webinst* sans passer par les dépôts de paquets (voir les annexes pour la [description des fichiers webinst][#module]).
    Lors de l'utilisation de ce menu, il vous sera demandé d'uploader le fichier webinst, puis de choisir entre les scénarios d'installation et de mise à jour du module.  
    Cette action ne devrait pas être utilisée en dehors des phases de développement.

Create Archive
:   Permet d'archiver le contexte (voir [la gestion des archives](#manage-context-archivage-management))

Delete context
:   Permet de supprimer le contexte et ses éléments associés (base de donnée, vault, etc.)

### Installed {#manex-ref:86592afd-a15e-4b5f-b5ed-0ce7a02c0284}

Cette section présente la liste des modules qui sont installés dans le contexte.
Lorsqu'un module a une mise-à-jour de disponible sur les dépôts de paquets, une icone apparaît en début de ligne d'un module et la nouvelle version disponible est présentée dans la colonne *Available Version*.
Pour mettre à jour le module, il faut alors cocher la ligne du module et cliquer sur le bouton *[Upgrade Selection]*.

### Available {#manex-ref:3af21351-6110-489d-be87-ba713ce506cd}
La section “Available” présente la liste des modules disponibles sur les dépôts de paquets.
Vous pouvez cocher les modules que vous souhaitez installer dans le contexte, et lancer l'installation en cliquant
sur le bouton *[Install Selection]*.

## Archivage et restauration de contexte {#manage-context-archivage-management}

Une archive de contexte contient tous les éléments d'un contexte (base de donnée, fichiers, etc.)
Ces archives peuvent permettre la restauration d'un contexte sur une autre machine.

**Attention** : Cette fonctionnalité ne doit pas se substituer à une sauvegarde réguière de vos données. c'est une commodité de développement (voir [Les procédures d'exploitation](#exploitation-save-restore)).

### Archivage {#manage-context-archivage}

Lors de l'utilisation du bouton [archivage], un assistant s'ouvre pour vous permettre de créer une archive.

![ Archivage ](i14.png "Archivage d'un contexte")

Il vous demande les éléments suivants :

| Champ         | Description                                                                         |
| -             | -                                                                                   |
| Name          | Nom de l'archive                                                                    |
| Description   | Description de l'archive                                                            |
| Exclude Vault | Si cette case est cochée, l'archive générée ne contiendra pas les fichiers du vault |

Le fichier généré sera une archive avec l'extension `.fcz` et stocké dans le sous-répertoire `archived-contexts` de dynacase-control. Cette archive contiendra :

* Les fichiers du contexte.
* Un dump de la base de données.
* Les fichiers des vaults (si demandé).

### Restauration {#manex-ref:170bfc5e-ef81-4e75-9cb3-88836ec2d12f}

Les archives `.fcz` présentes dans le sous-répertoire `archived-contexts` de dynacase-control sont présentés dans la section "Archives" de l'interface de dynacase-control, sous la liste "Context".

Pour restaurer une archive il faut cliquer sur le bouton *[Create Context]* et remplir les informations demandées :

| Champ                 | Description                                                                            |
| -                     | -                                                                                      |
| Root                  | Le chemin d'accès du répertoire dans lequel les fichiers du contexte seront restaurés. |
| Core Database Service | Le nom du service PostgreSQL dans lequel sera restauré le dump de la base de données.  |
| Vault Root            | Le chemin d'accès du répertoire dans lequel les fichiers du vault seront restaurés.    |

Ces répertoires doivent être créés manuellement et être accessibles en écriture à l'utilisateur Apache.

## Suppression d'un contexte {#manex-ref:e9f9ddf2-f6bb-40de-be19-e44d66e5397b}

Un contexte peut être supprimé via `Informations` > `Delete context`. Une
demande de confirmation est alors affichée afin de confirmer ou non l'exécution
de l'opération.

La suppression d'un contexte comporte deux phases :

* La suppression "_physique_" des éléments du contexte.
* La suppression "_logique_" pour supprimer le contexte de la liste des
  contextes gérés par dynacase-control.

La suppression "_physique_" d'un contexte supprime dans l'ordre les éléments
suivants :

* L'enregistrement de la crontab `FREEDOM/freedom.conf`.
* Le contenu des vaults du contexte.
* Le contenu de la base de données du contexte.
* Le contenu du répertoire racine du contexte.

Si la suppression d'un de ces éléments retourne une erreur, l'opération de
suppression se poursuit avec les éléments suivants, et à la fin de l'opération
un récapitulatif des erreurs rencontrés est affiché.

À la fin de l'opération de suppression "_physique_" (qu'il y ait eu des erreurs
ou non), le contexte est alors supprimé "_logiquement_" de la liste des
contextes gérés par dynacase-control.

## Enregistrement de contexte {#manex-ref:294bc6dc-9685-4d2a-92c5-3ed35dd2a8b3}

La procédure d'enregistrement d'un contexte peut vous être demandée dans le
cadre du support EEC.

* Les informations collectées sont consultables sur le contexte dans
  `Informations` > `Registration` > `Show configuration`.

![ Informations d'enregistrement du contexte ](i15.png "Consultation des informations d'enregistrement du contexte")

* Ces informations peuvent être téléchargés sous la forme d'une archive Zip sur
  le contexte dans `Informations` > `Registration` > `Download configuration`.

Les informations collectées sont :

* Informations générales :
  * date de collecte des informations ;
  * nombre de comptes utilisateur du contexte ;
  * version de dynacase-control ;
  * version de PHP ;
  * version de PostgreSQL.
* Liste des modules installés avec leur version.
* Informations système :
  * informations système (`uname -a`) du serveur ;
  * capacité mémoire du serveur ;
  * nombre de processeurs du serveur ;
  * caractéristiques des processeurs du serveur.

<!-- links -->
[module]: #manex-ref:f28ae532-05cf-4a2d-a959-fbf258f1a778