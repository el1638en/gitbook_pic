## Installation

En tant que super-utilisateur `root`, procéder comme suit :

  1. Installer `curl & apt-transport-https`

    ```
    root@vm-debian-1:~# apt -y install curl apt-transport-https
    ```

  2. Ajouter la clé du repository de Jenkins

    ```
    root@vm-debian-1:~# wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key|apt-key add -
    ```

  3. Ajouter le repository des packages stables de Jenkins et mettre à jour le système.

    ```
    root@vm-debian-1:~# sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
    root@vm-debian-1:~# apt update
    ```

  4. Télécharger la dernière version du package de Jenkins (changelog https://jenkins.io/changelog/) et l’installer.

    ```
    root@vm-debian-1:~# cd /tmp
    root@vm-debian-1:/tmp# wget https://pkg.jenkins.io/debian/binary/jenkins_2.146_all.deb
    root@vm-debian-1:/tmp# dpkg -i jenkins_2.146_all.deb
    root@vm-debian-1:/tmp# rm -f jenkins_2.146_all.deb
    ```
    Au cas où il manquerait des dépendances non installées sur le système, le gestionnaire de paquets l’indiquera.
    Il suffit d’installer ces dépendances et de relancer l’installation de Jenkins.

  5. Vérifier l'état du service Jenkins

    ```
    root@vm-debian-1:~# systemctl status jenkins.service
      ● jenkins.service - LSB: Start Jenkins at boot time
         Loaded: loaded (/etc/init.d/jenkins; generated; vendor preset: enabled)
         Active: active (exited) since Thu 2018-10-11 10:45:38 CEST; 2min 12s ago
         oct. 11 10:45:38 vm-debian-1 systemd[1]: Started LSB: Start Jenkins at boot time.
    ```

    Les logs de Jenkins sont disponibles dans le fichier ```/var/log/jenkins/jenkins.log```.

    Enfin, par défaut, Jenkins utilise le port 8080. Si celui-ci est déjà occupé par un
    autre serveur (Tomcat), Jenkins ne démarrera pas.
    Pour régler ce problème, il faut modifier le fichier de configuration ```/etc/default/jenkins```
    en remplaçant ```HTTP_PORT=8080``` par ```HTTP_PORT=8081```.
    Désormais, Jenkins utilisera le port 8081.

  6. Ouverture de port iptables (facultatif)

    Si un pare-feu  ```iptables``` est actif sur la machine, il faut ouvrir le port d’accès au serveur Jenkins

    ```
    root@vm-debian-1:~# iptables -A INPUT -p tcp -m tcp --dport 8080 -j ACCEPT -m comment --comment "TCP - Jenkins Input Trafic"
    ```    
