## Installation d'Ansible et de Molecule

Pour utiliser Ansible/Molécule dans un environnement Jenkins, il faut installer des 2 outils sur le slave Jenkins.
Si la machine Jenkins est aussi le slave, il faut y installer ces 2 outils.

  1. Installer Docker

  Pour installer Docker, consulter la documentation en ligne https://docs.docker.com/install/linux/docker-ce/debian/.

  Dans notre cas, Docker sera exécuté par l'utilisateur `jenkins` (utilisateur non-root). (https://docs.docker.com/install/linux/linux-postinstall/). Il faut ajouter l’utilisateur `jenkins` au groupe d’utilisateur `docker`.

  ```
  root@vm-debian-1:~# usermod -aG docker jenkins
  ```

  Il est conseillé de redémarrer le serveur pour la prise en compte de la modification.

  2. Installer pip, virtualenv et virtualenvwrapper

      1. Mettre à jour l’environnement

      ```
      root@vm-debian-1:~# apt update
      ```

      2. Installer le package `python-pip`

      ```
      root@vm-debian-1:~# apt install -y python-pip
      ```

      3. Installer `virtualenv`, `virtualenvwrapper` et `paramiko`

      ```
      root@vm-debian-1:~# pip install virtualenv virtualenvwrapper paramiko
      ```

      4. Ajouter les lignes ci-dessous dans le fichier `$HOME/.bashrc`

      Dans notre cas, `$HOME/.bashrc = /var/lib/jenkins/.bashrc`

      ```
      export WORKON_HOME=~/.virtualenvs
      export VIRTUALENVWRAPPER_PYTHON=/usr/bin/python
      export VIRTUALENVWRAPPER_VIRTUALENV=/usr/local/bin/virtualenv
      mkdir -p $WORKON_HOME
      source /usr/local/bin/virtualenvwrapper.sh
      ```

      5. Charger les modifications

      ```
      root@vm-debian-1:~# su - jenkins
      jenkins@vm-debian-1:~$ source /var/lib/jenkins/.bashrc
      ```
