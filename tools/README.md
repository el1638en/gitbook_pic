# JDK

  1. Mettre à jour l'Environnement

  ```
  root@vm-debian-1:~# apt update
  ```

  2. Installer le package `openjdk-8-jdk`

  ```
  root@vm-debian-1:~# apt install -y openjdk-8-jdk
  ```

  3. Créer la variable d'environnement `JAVA_HOME` en ajoutant la ligne ci-dessous au fichier `/etc/environment`:

  ```
  JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
  ```

  4. Recharger le fichier `/etc/environment`:

  ```
  root@vm-debian-1:~# source /etc/environment
```

# Maven

  1. Téléchargement de l'archive de Maven disponible à https://maven.apache.org/download.cgi

  ```
  root@vm-debian-1:~# mkdir /opt/maven
  root@vm-debian-1:~# cd /opt/maven
  root@vm-debian-1:/opt/maven# wget http://mirror.ibcp.fr/pub/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz
  ```

  2. Dézipper l'archive

  ```
  root@vm-debian-1:/opt/maven# tar zxvf apache-maven-3.6.0-bin.tar.gz
  ```

  Vous devriez obtenir un nouveau répertoire `/opt/maven/apache-maven-3.6.0`.

  3. Variable d'environnement `M3_HOME`

  Définir la variable `M3_HOME` dans le fichier `/etc/profile.d/maven.sh`. Pour cela, éditer le fichier `/etc/profile.d/maven.sh` et y ajouter le contenu ci-dessous :

  ```
  # Maven Home
  M3_HOME="/opt/maven/apache-maven-3.6.0"
  export M3_HOME
  # Update PATH
  PATH=$PATH:"$M3_HOME/bin"
  export PATH
  ```

  Pour que le système prenne en compte cette nouvelle variable, taper la commande :

  ```
  root@vm-debian-1:~# source /etc/profile
  ```

  4. Test

    - Tester la commande `mvn` :

      ```
      root@vm-debian-1:~# mvn -version
      Maven home: /opt/maven/apache-maven-3.6.0
      ```

    - Tester la variable d'environnement `M3_HOME` :

      ```
      root@vm-debian-1:~# echo $M3_HOME
      /opt/maven/apache-maven-3.6.0
      ```

    - Vérifier que le sous-répertoire  `/opt/maven/apache-maven-3.6.0/bin` est contenu dans la variable `$PATH` :

      ```
      root@vm-debian-1:~# echo $PATH
      usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/maven/apache-maven-3.6.0/bin
      ```

# Gradle

  1. Téléchargement de l'archive de Gradle (https://gradle.org/install/)

  ```
  root@vm-debian-1:~# mkdir /opt/gradle
  root@vm-debian-1:~# cd /opt/gradle
  root@vm-debian-1:/opt/gradle# wget https://services.gradle.org/distributions/gradle-5.1.1-all.zip
  ```

  2. Dézipper l'archive

  ```
  root@vm-debian-1:/opt/gradle# unzip gradle-5.1.1-all.zip -d .
  ```

  Vous devriez obtenir un nouveau répertoire `/opt/gradle/gradle-5.1.1`

  3. Variable d'environnement `GRADLE_HOME`

  Définir la variable `GRADLE_HOME` dans le fichier `/etc/profile.d/gradle.sh`. Pour cela, éditer le fichier `/etc/profile.d/gradle.sh` et y ajouter le contenu ci-dessous :

  ```
  # Gradle Home
  GRADLE_HOME="/opt/gradle/gradle-5.1.1"
  export GRADLE_HOME
  PATH=$PATH:"$GRADLE_HOME/bin"
  export PATH
  ```

  Pour que le système prenne en compte cette nouvelle variable, taper la commande :

  ```
  root@vm-debian-1:~# source /etc/profile
  ```

  4. Test

    - Tester la commande `gradle` :

      ```
      root@vm-debian-1:~# gradle -v
      ------------------------------------------------------------
      Gradle 5.1.1
      ------------------------------------------------------------
      ```

    - Tester la variable d'environnement `GRADLE_HOME` :

      ```
      root@vm-debian-1:~# echo $GRADLE_HOME
      /opt/gradle/gradle-5.1.1
      ```

    - Vérifier que le sous-répertoire  `/opt/gradle/gradle-5.1.1/bin` est contenu dans la variable `$PATH` :

      ```
      root@vm-debian-1:~# echo $PATH
      usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/opt/gradle/gradle-5.1.1/bin
      ```
