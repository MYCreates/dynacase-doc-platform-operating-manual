# FreeDAV/WebDAV {#core-ref:fc665742-8616-41cd-9448-452801333408}

## Présentation de l'application `DAV` {#core-ref:5362ffa9-bcc9-4dc1-aeb3-a8fe563606a5}

Le protocole [WebDAV][WebDAV] permet aux utilisateurs d'éditer et de sauver les
fichiers inclus dans les documents directement depuis leur traitement de texte,
tableurs et autres logiciels d'édition de fichiers.

L'application `DAV` de Dynacase gère deux serveurs [WebDAV][WebDAV] :

* un serveur authentifié par login et mot de passe, pour une utilisation
classique au travers de client WebDAV tel que l'explorateur de fichier Microsoft
Windows ;
* un serveur authentifié par identifiant de sessions pour être utilisé à travers
l'interface Web d'échange de fichiers.

Lors de la modification de fichiers par l'interface Web, l'éditeur de fichier
utilise le serveur WebDAV authentifié par numéro de session. L'adresse de ce
serveur WebDAV doit être précisé dans le paramètre applicatif [`FREEDAV_SERVEUR`][FREEDAV_SERVEUR].

L'adresse du deuxième serveur doit être renseigné dans le paramètre
[`WEBDAV_SERVEUR`][WEBDAV_SERVEUR] (accessible depuis le menu
administration/paramètres de configuration/paramètres applicatif/dav).

Ces deux serveurs doivent avoir deux noms distincts du nom de la machine pour
l'accès à l'interface web. Ces deux noms désignent la même machine (alias DNS)
que le serveur WEB. Le serveur Web Apache doit être configuré pour avoir deux
hôtes virtuels (Apache [`VirtualHost`][apache_vhosts]) correspondant aux deux
serveurs DAV.

## Configuration des postes client éditer et sauver en ligne {#core-ref:bbd74861-5f0c-496a-8452-034614896be2}

### Windows {#core-ref:bb4bed72-e18f-47cb-8bb3-5b4a7a783de4}

Le navigateur Web ne peut pas nativement exécuter un autre programme. Pour
autoriser votre navigateur à ouvrir les éditeurs vous devez installer deux
fichiers sur votre poste client `opendav.reg` et `opendav.vbs` :

* <https://eec.anakeen.com/public/dav/windows/opendav.reg>
* <https://eec.anakeen.com/public/dav/windows/opendav.vbs>

Pour enregistrer le protocole pour le navigateur, il faut double-cliquer sur
l'icône du fichier `opendav.reg` que vous avez téléchargé. Ensuite il faut
enregistrer le fichier `opendav.vbs` dans le répertoire `C:\WINDOWS\`.

Après cette manipulation à partir de votre navigateur Internet Explorer ou
Firefox, vous pouvez éditer et enregistrer les fichiers comme s'ils étaient sur
votre disque dur.

Lorsque vous cliquez sur le lien `Ouvrir dans un éditeur`, une interface vous
demande de choisir le programme capable de lire le fichier. Pour enregistrer le
fichier sur le serveur, une fois les modifications effectuées, il suffit
d'utiliser le menu enregistrer de l'éditeur comme d'habitude

Notes :

* Une mise à jour du client WebDAV/WebFolders de Windows est disponible, et
corrige apparemment plusieurs problèmes comme la gestion des caractères
accentués : <http://support.microsoft.com/kb/907306>
* Si votre accès Internet se fait via un proxy, il peut être nécessaire de
modifier la configuration de votre poste.

### Linux {#core-ref:e1f3b16e-1b32-4204-81a6-1e3411b44c1a}

Dans Firefox, entrez l'URL : `about:config`

Ajouter les options suivantes :

* [bouton droit > Nouvelle > Valeur booléenne]
* nom : `network.protocol-handler.external.asdav`
* valeur : `true`
* [bouton droit > Nouvelle > Chaîne de caractère]
* nom : `network.protocol-handler.app.asdav`
* valeur : `dynacase-opendav.sh`

Pour Firefox > 3.5, il faut aussi lancer deux commande à l'aide de gconftool-2 :

    gconftool-2 -s /desktop/gnome/url-handlers/asdav/command '/path/to/app %s' --type String
    gconftool-2 -s /desktop/gnome/url-handlers/asdav/enabled --type Boolean true

Créer le fichier de commande `dynacase-opendav.sh` suivant dans un répertoire
accessible par le `PATH` système (`/usr/local/bin/dynacase-opendav.sh` par
ex.) :

    #!/bin/bash
    np=$(echo "$1" | sed "s/asdav:/http:/")
    ooffice "$np"

Rendre le script exécutable :

    chmod +x /usr/local/bin/dynacase-opendav.sh

Il reçoit en argument l'URL du fichier à éditer. Cette URL est une ressource
`asdav:` qui doit être transformée en `http:` et être fournie à l'exécutable
Open Office.

Si la configuration n'est pas correcte, vous allez rencontrer ce message :

![](dav3.png)

Si tout fonctionne correctement, vous allez avoir cette fenêtre :

![](dav4.png)

### Mac OS X {#core-ref:1ee08b2c-b103-4da4-830d-087f9dc4253d}

Télécharger et installer l'application `Asdav.app` :

* <https://eec.anakeen.com/public/dav/macosx/Dynacase%20Asdav.dmg>

L'application `Asdav.app` permet d'ouvrir les URLs `asdav:` de Dynacase avec
OpenOffice, et donc d'éditer en ligne tous les types de fichiers supportés par
celui-ci.

`Asdav.app` utilisera en premier `OpenOffice.org.app`, et si celui-ci n'est pas
disponible il utilisera `NeoOffice.org.app`.

Note pour les utilisateurs sous Mac OS X Tiger (10.4) :

OpenOffice.org.app 3.0.0 comporte un petit bug qui le rend inutilisable
lorsqu'il est lancé par Asdav.app (c.f.
<http://www.openoffice.org/issues/show_bug.cgi?id=93731>).

Pour contourner ce problème, il faut éditer le fichier
`/Applications/OpenOffice.org.app/Contents/Info.plist` et changer la valeur de
la clef `CFBundleExecutable` par `soffice.bin` :

    [...]
    <key>CFBundleExecutable</key>
    <string>soffice.bin</string>
    [...]

Note : ce problème doit être à présent résolu à partir de OpenOffice.org 3.0.1

## Configuration serveur web apache {#core-ref:f3c50808-5b1c-43a5-b537-a0981004945d}

L'utilisation de la fonction DAV nécessite la création de deux configurations
Apache à base de VirtualHosts. Comme cette conf n'est pas accessible et
réalisable par dynacase-control, vous devrez insérer les configurations données
ci-dessous dans la configuration de votre serveur Apache.

### Pré-requis {#core-ref:3d9c8431-99c8-4065-8a62-1d042be4f826}

Ce module nécessite en pré-requis l'activation du module `rewrite` :

    # a2enmod rewrite
    # /etc/init.d/apache2 restart

### Base de données webdav {#core-ref:74fb8845-4557-44b5-8bab-55dfa870c66f}

L'utilisation du protocole dav utilise un schéma `dav` dédié dans la base
données générale.

Les tables `dav.locks`, `dav.properties` et `dav.sessions` sont automatiquement
crées lors de l'installation de dynacase-core.

### VirtualHosts Apache pour l'accès aux fonctions WebDAV {#core-ref:56de6612-c48d-4ffb-9254-3cd5ba5d7d1c}

Pour utiliser les fonctions d'édition et de montage WebDAV, vous devez mettre en
place deux VirtualHosts Apache.

Pour cela, il vous faut déclarer deux noms DNS additionnels qui pointeront vers
votre serveur Dynacase.

Exemple :

Soit un contexte dynacase installé dans le répertoire `/var/www/ged`, et
accessible par l'enregistrement DNS `ged.example.net`.

Vous pouvez créer deux enregistrements DNS `ged-freedav.example.net` et
`ged-webdav.example.net` qui pointeront vers `ged.example.net`.

    ged-freedav IN CNAME ged.example.net.
    ged-webdav  IN CNAME ged.example.net.

Dans ce cas, les configurations associées seront :

* Configuration pour l'accès FreeDAV (édition en ligne) :

Fichier de configuration `/etc/apache2/sites-available/default-freedav`

    <VirtualHost *:80>
        ServerName ged-freedav.example.net
    
        DocumentRoot /var/www/ged/freedav
    
        <Directory /var/www/ged/freedav/>
                Order allow,deny
                Allow from All
    
                DirectoryIndex index.php
    
                Options FollowSymLinks
                AllowOverride All
        </Directory>
    
        ErrorLog /var/log/apache2/freedav.error.log
    </VirtualHost>

Activer la configuration

    # a2ensite ged-freedav
    # /etc/init.d/apache2 restart

* Configuration pour l'accès WebDAV (montage comme système de fichier) :

Fichier de configuration `/etc/apache2/sites-available/ged-webdav`

    <VirtualHost *:80>
        ServerName ged-webdav.example.net
    
        DocumentRoot /var/www/ged/webdav
    
        <Directory /var/www/ged/webdav/>
                Order allow,deny
                Allow from All
    
                DirectoryIndex index.php
    
                Options FollowSymLinks
                AllowOverride All
        </Directory>
    
        ErrorLog /var/log/apache2/webdav.error.log
    </VirtualHost>

Activer la configuration

    # a2ensite ged-webdav
    # /etc/init.d/apache2 restart

Remarque : Il est possible de vérifier avec votre navigateur que les deux
VirtualHost fonctionnent correctement :

* `http://ged-freedav.example.net` : Doit afficher une page blanche ;
* `http://ged-webdav.example.net` : Doit demander un mot de passe et afficher
“WebDAV Server: HTML view is not implemented yet”.

## Paramétrage de l'application dav {#core-ref:a87094d7-e5b1-49c7-9086-35489d2b69d3}

L'application dav comporte quatre paramètres accessibles via :

* menu “Administration”
* onglet “Paramètres applicatifs”
* section “Dav”

`FREEDAV_SERVEUR`
:   adresse du serveur webdav pour freedav. Alias de nom de la machine serveur.
    Le virtual host associé ne doit pas être authentifié.

`WEBDAV_SERVEUR`
:   adresse du serveur webdav pour le serveur authentifié. Alias de nom de la
    machine serveur. Le virtual host associé doit être authentifié.

`WEBDAV_DB`
:   coordonnées d'accès à la base de données webdav. Doit être égale
    aux coordonnées de la base principale - automatiquement renseignée lors de
    l'installation.

`WEBDAV_ROOTID`
:   Identifiant du dossier racine pour l'accès authentifié. Identifiant du
    document dossier pour la racine du webdav authentifié. Lors d'un usage
    combiné avec WORKSPACE, la référence désigne un document recherche simple
    qui recherche l'ensemble des espaces de travail.

`WEBDAV_FOLDERMAXITEM`
:   Nombre maximum d'éléments (dossier/fichier) visibles dans un dossier. Cette
    limite permet d'éviter d'afficher trop d'éléments pouvant provoquer un
    ralentissement du client.

## Test de débogage du dav {#core-ref:c1ef9917-3693-4b77-88c6-b3dcef521f97}

En cas de problème dans la mise en place de l'application dav, il est possible
d'effectuer différents tests pour identifier la source du problème.

### Test avec cadaver

Le client WebDAV `cadaver`e n ligne de commande disponible sous Linux permet de
vérifier que la connexion au serveur webdav et au serveur freedav fonctionne
correctement :

    $ cadaver ged-freedav
    dav:/>

    $ cadaver ged-webdav
    Authentication required for  on server `ged-webdav':
    Username: admin
    Password: ******
    dav:/> ls
    Listing collection `/': succeeded.
    Coll:   import                                 0  sep 17 16:25
    Coll:   la poubelle                            0  sep 17 16:25
    Coll:   les cycles                             0  sep 17 16:25
    Coll:   les familles                           0  sep 17 16:25
    Coll:   les maisons                            0  sep 17 16:25
    Coll:   les profils                            0  sep 17 16:25
    dav:/>

En cas de problèmes, regarder les éventuels messages d'erreur le error_log PHP
ou Apache.

## Troubleshoot de l'application dav {#core-ref:382b6c42-5641-4df3-9f30-e52164138c20}

### Delocker un document {#core-ref:23fe9936-2119-4739-bfbb-30fb2b27f8c7}

Situation : Le document a été chargé en édition avec OpenOffice.org, et ce
dernier s'est terminé anormalement en laissant le document locké au niveau
WebDAV.

Solution : Identifier l'URL du document (ex.
`asdav:/ /freedav.example.net/freedav/vid-12765-4870-3a0404dfe29f7c88bb58ac8a6943559d/Foo.odt`),
et supprimer le lock du document dans la table locks :

    # PGSERVICE=dynacase psql \
    -c "DELETE FROM dav.locks WHERE path = '/freedav/vid-12765-4870-3a0404dfe29f7c88bb58ac8a6943559d/Foo.odt'"

### Lenteur d'ouverture des fichiers {#core-ref:8ac94034-acdb-4a46-beaa-d08e56817df3}

Il arrive sur certains postes en Windows XP d'avoir des lenteurs d'accès aux
documents : les temps d'accès constatés sont d'environ 30 secondes.

Ces problèmes sont inhérents à Windows, il existe 2 solutions possibles pour
pallier à ces lenteurs :

#### 1. Solution pour intranet (modification du serveur dynacase) {#core-ref:ce27c195-948b-43df-89e6-8b67e4b5a1c3}

L'installation du service Samba (sur le serveur Dynacase) semble réduire dans
certains cas le temps d'attente du poste Windows pour l'accès aux ressources
WebDAV.

#### 2. Solution pour tous les cas (modification de chaque poste client)

Dans certains cas, le changement d'ordre des "Fournisseurs de réseau" peut
réduire le temps d'attente du poste Windows pour l'accès aux ressources WebDAV.

Pour cela, allez dans le panneau de configuration puis dans Connexions réseau /
Avancé / Paramètres avancés :

![](dav_pb1.png)

Dans l'onglet “Ordre des fournisseurs”, vous placez “Web Client Network” en tête
de liste :

![](dav_pb2.png)

Explication : lorsque vous cherchez à accéder à un serveur dav, le fournisseur
réseau du dav devient prioritaire sur les autres services réseau, ce qui évite
les problématiques de timeouts.

Par contre cela peut potentiellement engendrer d'autres lenteurs pour accéder à
d'autres ressources réseau (serveur de fichier, active directory, …).

Cette solution est à appliquer au cas par cas.

<!-- links -->

[WebDAV]: http://fr.wikipedia.org/wiki/WebDAV
[FREEDAV_SERVEUR]: #core-ref:a87094d7-e5b1-49c7-9086-35489d2b69d3
[WEBDAV_SERVEUR]: #core-ref:a87094d7-e5b1-49c7-9086-35489d2b69d3
[apache_vhosts]: http://httpd.apache.org/docs/2.2/vhosts/
