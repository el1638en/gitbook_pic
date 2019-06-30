## Gestion des noeuds

Pour exécuter un jobs Jenkins, on peut utiliser la machine Jenkins(noeud principal) elle-même
ou utiliser des noeuds distants.

Pour configurer un noeud distant (par exemple machine `slave_1`), procéder comme suit :

  1. Création  un utilisateur dédié au job Jenkins

    Sur la machine `slave_1`, en tant que super-utilisateur `root`, ajouter l'utilisateur `deploy` :

    ```
    root@slave_1:~# adduser deploy
    Ajout de l'utilisateur « deploy » ...
    Ajout du nouveau groupe « deploy » (1007) ...
    Ajout du nouvel utilisateur « deploy » (1006) avec le groupe « deploy » ...
    Création du répertoire personnel « /home/deploy »...
    Copie des fichiers depuis « /etc/skel »...
    Entrez le nouveau mot de passe UNIX :
    Retapez le nouveau mot de passe UNIX :
    passwd : le mot de passe a été mis à jour avec succès
    Modification des informations relatives à l'utilisateur deploy
    Entrez la nouvelle valeur ou « Entrée » pour conserver la valeur proposée
            Nom complet []:
            N° de bureau []:
            Téléphone professionnel []:
            Téléphone personnel []:
            Autre []:
    Cette information est-elle correcte ? [O/n]o
    root@slave_1:~#
    ```

  2. Générer une paire de clés SSH pour le nouvel utilisateur

    En tant qu'utilisateur `deploy`, créer une paire de clés SSH.

    ```
    root@slave_1:~# su - deploy
    deploy@slave_1:~$ ssh-keygen -t rsa -b 4096
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/deploy/.ssh/id_rsa):
    Created directory '/home/deploy/.ssh'.
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /home/deploy/.ssh/id_rsa.
    Your public key has been saved in /home/deploy/.ssh/id_rsa.pub.
    The key fingerprint is:
    c4:7a:85:4a:7b:d3:f4:ff:66:f9:b6:e8:37:56:7b:76 deploy@vm-debian-1
    The key's randomart image is:
    +---[RSA 4096]----+
    |                 |
    |       . .       |
    |      . + o      |
    |     . = + .     |
    |      + S . .    |
    |       o .   .  .|
    |              . +|
    |              .BE|
    |            .oo*O|
    +-----------------+
    deploy@slave_1:~$ cat ~/.ssh/id_rsa.pub > ~/.ssh/authorized_keys
    ```

    Copier la clé privée de façon temporaire.

    ```
    deploy@slave_1:~$ cat ~/.ssh/id_rsa
    ```

  3. Initialiser le compte de l'utilisateur `deploy` sur le serveur Jenkins.

    Rendez-vous sur la page d'accueil de Jenkins et cliquer sur «Identifiants ⇒ System ⇒ Identifiants globaux(illimités) ⇒ Ajouter des identifiants».

      - Type : choisir le type «SSH Username with private key»
      - Portée: choisir «Globale(Jenkins, esclave, items, etc...)»
      - Nom d'utilisateur : «deploy». C'est le nom d'utilisateur créé sur la machine esclave distante.
      - Private Key :
          - Enter directly : Il faut y copier le contenu de la clé privée que nous avons précédemment stockée dans un fichier temporaire.
          - Passphrase : saisir la passphrase utilisée pour protéger la clé privée.
          - ID : «jenkins_deploy_slave_1»
          - Description : «User deploy pour se connecter à l'esclave slave_1»

  4. Création du noeud esclave sur le serveur Jenkins.

  Rendez-vous sur la page d'administration de Jenkins (lien «Administrer Jenkins») ou en tapant le mot-clé «manage» dans la boîte de recherche. Cliquer sur «Gérer les nœuds ⇒ Créer un nouveau nœud».

      - Nom : «slave_1»
      - Description : «Noeud slave_1»
      - Répertoire de travail : «/home/deploy», homedir de l'utilisateur «deploy»
      - Utilisation : «Utiliser cet esclave autant que possible»
      - Méthode de lancement : «Launch slave agents on Unix machines via SSH1»
      - Host : 192.168.0.16 : c'est l'adresse IP ou le DNS du noeud.
      - Credentials : Sélectionner l'utilisateur «deploy» que nous avons créé précédemment.
