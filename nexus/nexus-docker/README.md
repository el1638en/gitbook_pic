## Nexus dans un container Docker

  1. Récupération de l’image du container Docker

    ```
    root@vm-debian-1:~# docker pull sonatype/nexus3
    root@vm-debian-1:~# docker images                                                     
    REPOSITORY    TAG     IMAGE ID   CREATED SIZE                                                           
    sonatype/nexus3       latest              f2014d39f023        5 weeks ago         509MB
    ```

  2. Création d’un répertoire

    Il faut créer un répertoire local sur lequel il faut monter le container docker.

    ```
    root@vm-debian-1:~# mkdir /opt/nexus-data
    root@vm-debian-1:~# chmod -R 200 /opt/nexus-data
    ```

  3. Démarrage du container Docker

    ```
    root@vm-debian-1:~# docker run -d -p 8081:8081 --name nexus3 -v /opt/nexus-data:/nexus-data:rw sonatype/nexus3
    ```
