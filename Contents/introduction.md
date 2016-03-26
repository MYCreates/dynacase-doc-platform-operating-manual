# Introduction {#manex-ref:5bd3428c-f12e-4c77-9ecb-92988f55b06c}

## Abstract {#manex-ref:76586fb4-24a3-4303-93ac-5fc11149aef3}

Ce document décrit les procédures d'installation et de mise à jour de Dynacase
Platform, et présente les principales procédures d'exploitation.

## Présentation {#manex-ref:b4beabfd-093d-4c99-945d-d836f8631231}

L'installation d'un contexte Dynacase nécessite au préalable l'installation de
Dynacase Control qui permet de créer et de gérer les contextes Dynacase. Dans
un contexte Dynacase vous pouvez installer Dynacase Platform et les modules
spécifiques à votre application.

## Historique des modifications {#manex-ref:2750f605-a6bc-4b29-9d3a-9728fef45f5e}

### Édition 11 {#manex-ref:e9fd06c3-3ce2-49ab-9216-392a59b3068d}

* Mise à jour [pré-requis](#manex-ref:1636e34c-7f63-4cc7-84d2-721c68d69475) PHP {5.4, 5.5, 5.6} et Pg {9.1, 9.2, 9.3, 9.4}
* Restrictions sur le nom d'un paquet `webinst` et le nom d'un module dans [Format des modules webinst](#manex-ref:f28ae532-05cf-4a2d-a959-fbf258f1a778)
* Ré-initialisation de `CORE_TMPDIR` et `FREEDOM_UPLOADDIR` lors de la [Restauration](#manex-ref:170bfc5e-ef81-4e75-9cb3-88836ec2d12f) d'un contexte
* Ajout [Enregistrement de contexte](#manex-ref:294bc6dc-9685-4d2a-92c5-3ed35dd2a8b3)
* Mise à jour [Check base documentaire](#manex-ref:4504ec92-23b6-4c70-af16-923ebc29e5f4)
* Espace disque minimal requis pour [Archiver un contexte](#manex-ref:6dd36914-0dd0-43fd-bfde-f90174642f2a)
* Paramètre WIFF [`connect-timeout`](#manex-ref:d6ee7ebc-0711-49e2-b776-f4e9077683af)
* Ajout [Suppression d'un contexte](#manex-ref:e9f9ddf2-f6bb-40de-be19-e44d66e5397b) par l'interface Web

### Édition 10 {#manex-ref:3cba4c25-ed84-429f-a6d4-ea460a98e4be}

* Mise à jour [pré-requis](#manex-ref:1636e34c-7f63-4cc7-84d2-721c68d69475) Firefox &gt;= 4.0.1

### Édition 9 {#manex-ref:433308c7-d097-4865-b0c2-885286e989b6}

* Mise à jour [pré-requis PHP](#manex-ref:1f48eb68-2268-45b7-8281-259ec22d4802) et [PostgreSQL](#manex-ref:6446ba24-a9ae-4c1b-a84c-f31df81bcea8)
* Mise à jour `post-restore` du [workflow des opérations](#manex-ref:a85f6c53-baa9-4f8e-8f40-f01784843808) des modules webinst
* Précision sur la [comparaison des versions](#manex-ref:d168e9ea-78dc-4e23-b2a6-814bee0e4e2e) de modules webinst
* Précision sur les variables [`MODULE_VERSION_{TO,FROM}`](#manex-ref:afc3d392-bd87-418a-af0e-ceb8924e74a2) dans les programmes personnalisés

### Édition 8 {#manex-ref:4a20c3da-65b2-41f6-9ccf-314e99c7d7e5}

* Ajout [Journal des messages d'erreurs](#manex-ref:022e6b2f-cbe7-4ead-8f84-3ed8d0d718c9)
* Ajout commande CLI pour [Créer un nouveau contexte](#manex-ref:e1011c80-4563-4df0-858a-29f49e6582c6)
* Ajout commande CLI pour [Manipuler les propriétés d'un contexte](#manex-ref:565ae938-49ca-403d-8fa7-f7109463601b)
* Ajout commande CLI pour [Modifier les dépôts de paquets utilisés par un contexte](#manex-ref:c1a293b8-a043-44da-b2c4-2cc1c70375fa)
* Ajout commande CLI pour [Supprimer un contexte](#manex-ref:186bb0a7-f7b2-49da-9a84-9af5a0a5d306)
* Ajout commande CLI pour [Gérer la liste des dépôts de paquets de dynacase-control](#manex-ref:a07e974e-bc71-498a-97ca-9b4f7dcf4c1e)
* Ajout commande CLI pour [Gérer les paramètres de dynacase-control](#manex-ref:ec9ba402-bf8a-42e3-bd61-2899bc583c87)
* Ajout commande CLI pour [Enregistrer dynacase-control avec son compte EEC](#manex-ref:15a76a3f-186c-4af9-9daf-75dc252d9235)
* Ajout commande CLI pour [Enregistrer un contexte avec son compte EEC](#manex-ref:f1983ab4-4a45-4f0a-91b8-c903e02c89ac)
* Modification [Format des modules webinst](#manex-ref:f28ae532-05cf-4a2d-a959-fbf258f1a778) pour la validation XML (Voir [Changements par rapport à l'ancien format](#manex-ref:8005debe-6796-4c93-bf66-78889b153bfb))

### Édition 7 {#manex-ref:94da8570-5c57-4a07-8fca-101dfec3394e}

* précision des [versions des modules PEAR supportés](#manex-ref:5987ddd7-fd7f-4bcd-8ad6-1d9cafadd8a6)  
* versions  [PHP](#manex-ref:1f48eb68-2268-45b7-8281-259ec22d4802), [PostgreSQL](#manex-ref:6446ba24-a9ae-4c1b-a84c-f31df81bcea8) et du [navigateur IE](#manex-ref:1636e34c-7f63-4cc7-84d2-721c68d69475)
* ajout chapitre [Ajustement des références à la machine](#manex-ref:042483fb-9812-4df3-9829-ff3970767e29)

### Édition 6 {#manex-ref:68d657b1-384d-4d80-884a-c212d863f8db}

* précision des [versions des modules PEAR supportés](#manex-ref:5987ddd7-fd7f-4bcd-8ad6-1d9cafadd8a6)
