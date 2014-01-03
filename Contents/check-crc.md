# Vérification de l'intégrité des éléments téléchargés {#controle-integrite}

L'intégrité des paquets Control (webinst), archives compressées ou description de l'index du dépôt (content.xml) peut être contrôlée.

Après avoir téléchargé le contenu du dépôt vous pouvez, avant de procéder à une installation, vérifier qu'il est intègre.

## Téléchargement des sommes de contrôle {#manex-ref:b3e52cf9-ded7-4de1-99eb-1f41d65531eb}

Le fichier `SHA256sum.asc`, téléchargé, contient les sommes de contrôles. Vous devez d'abord vous assurer de son origine (qui l'a produit) :

* récupérez la clé publique Anakeen Dynacase

    # gpg --recv-keys 9AEA83AF
    gpg: requête de la clé 9AEA83AF du serveur hkp keys.gnupg.net
    gpg: clé 9AEA83AF: « Dynacase distribution <labs@anakeen.com> » n'a pas changé
    gpg: Quantité totale traitée: 1
	
* vous pouvez vous assurer que la clé récupérée est bien celle attendue en comparant son empreinte avec [celle se trouvant sur cette page](https://docs.anakeen.com/).

    # gpg --fingerprint 9AEA83AF
    pub   2048R/9AEA83AF 2013-03-15
          Empreinte de la clé = D41B 24B6 F276 3E38 1A25  29FF 1633 12B4 9AEA 83AF
    uid                  Dynacase distribution <labs@anakeen.com>
    sub   2048R/361B4EAC 2013-03-15

* vous pouvez donner votre confiance à cette clé avec la commande `gpg --edit_keys`.

* vérifiez que le fichier des sommes de contrôle est correctement signé 

    # gpg --verify SHA256sum.asc
    gpg: Signature faite le ven. 15 mars 2013 17:07:18 CET avec la clé RSA ID 9AEA83AF
    gpg: Bonne signature de « Dynacase distribution <labs@anakeen.com> »
    gpg: ATTENTION: Cette clé n'est pas certifiée avec une signature de confiance !
    gpg:          Rien ne dit que la signature appartient à son propriétaire.
    Empreinte de clé principale: D41B 24B6 F276 3E38 1A25  29FF 1633 12B4 9AEA 83AF

Vous êtes maintenant en possession d'un fichier contenant les sommes de contrôles des éléments téléchargés que vous pouvez considérer comme fiables.

## Vérification des sommes de contrôles {#manex-ref:c8b9aecf-331c-4493-9d87-5157977f1567}

Dans le répertoire de téléchargement des éléments (paquets, archives, etc.) et du fichier SHA256sum.asc, lancez la commande suivante :

     # sha256sum ---check SHA256sum.asc 
     (ou)
     # shasum -a 256 --check SHA256sum.asc
	
Elle vous indique pour chacun des éléments téléchargés s'il correspond à la somme de contrôle attendue (s'il est intègre).
