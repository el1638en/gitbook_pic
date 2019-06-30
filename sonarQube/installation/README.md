## Installation

La dernière version stable de SonarQube est disponible [ici](https://www.sonarqube.org/downloads/).

La documentation officielle d'installation est disponible [ici](https://docs.sonarqube.org/latest/).

  1. CRéer un utilisateur `sonar` et télécharger l’archive de SonarQube.

    En tant que super-utilisateur `root` :

    - créer un nouvel utilisateur `sonar`

    ```
    root@vm-debian-1:~# adduser sonar
    ```

    - télécharger l'archive, le dézipper et attribuer les droits à l'utilisateur `sonar`

    ```
    root@vm-debian-1:~# cd /opt
    root@st-debian:/opt# wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-7.8.zip
    root@st-debian:/opt# unzip sonarqube-7.8.zip
    root@st-debian:/opt# chown -R sonar:sonar sonarqube-7.8
    ```

  2. Modification du script `sonar.sh`

    Dans le fichier `/opt/sonarqube-7.8/bin/linux-x86-64/sonar.sh`, remplacer

    ```
    `#RUN_AS_USER=`
    ```

    par

    ```
    `RUN_AS_USER=sonar`.
    ```
  3. Port de SonarQube

    Par défaut, le port de connection à SonarQube est 8080. Si vous souhaitez le modifier, modifier le fichier
    `/opt/sonarqube-7.8/conf/sonar.properties` en remplacant la ligne `sonar.web.port=9000` par `sonar.web.port=prot_choisi` où `port_choisi` représente le port de substitution de votre choix.


  3. Configuration du service de SonarQube

    Pour démarrer SonarQube avec SystemD, il faut :

      1. créer le fichier `/lib/systemd/system/sonarqube.service`

      ```
      root@vm-debian-1:~# touch /lib/systemd/system/sonarqube.service
      ```

      2. y copier le contenu ci-dessous `vim /lib/systemd/system/sonarqube.service`

      ```
      [Unit]
      Description=SonarQube 7.8 service
      After=syslog.target network.target

      [Service]
      ExecStart=/opt/sonarqube-7.8/bin/linux-x86-64/sonar.sh start
      ExecStop=/opt/sonarqube-7.8/bin/linux-x86-64/sonar.sh stop
      ExecReload=/opt/sonarqube-7.8/bin/linux-x86-64/sonar.sh restart
      Type=forking
      User=sonar
      Group=sonar

      [Install]
      WantedBy=multi-user.target
      ```

    3. activer le service SonarQube

      ```
      root@vm-debian-1:~# systemctl enable sonarqube.service
      ```

  4. Configuration de la base de données

      SonarQube a besoin d'une BDD pour stocker les résultats des messures. Pour cela, nous allons créer une base de données
      PostgreSQL.

    1. Se connecter à Postgres et créer  l'utilisateur `sonar` ayant pour mot de passe `sonar` et une base de données `sonar`.

      ```
      root@vm-debian-1:~# su - postgres
      postgres@vm-debian-1:~$ psql
      psql (9.6.13)
      Saisissez « help » pour l'aide.

      postgres=# CREATE USER sonar LOGIN password 'sonar' CREATEROLE;
      CREATE ROLE
      postgres=# CREATE DATABASE sonar OWNER=sonar;
      CREATE DATABASE
      postgres=# \q
      ```

    2. Ajouter la base de données à la configuration de SonarQube

      Modifier le fichier `/opt/sonarqube-7.8/conf/sonar.properties` en remplaçant les lignes :

      ```
      #sonar.jdbc.username=
      #sonar.jdbc.password=
      #sonar.jdbc.url=
      ```

      par :

      ```
      sonar.jdbc.username=sonar
      sonar.jdbc.password=sonar
      sonar.jdbc.url=jdbc:postgresql://localhost/sonar
    ```

   3. Ouverture de port Iptables

      Si vous disposez d'un par-feu Iptables sur la machine, il faut ouvrir le port (9000 ou autre port choisi par l'admin pour SonarQube) de SonarQube.

      ```
      root@vm-debian-1:~#  iptables -t filter -A INPUT -p TCP --dport 9000 -j ACCEPT -m comment --comment "SonarQube port"
      ```
