## Reverse-proxy avec un serveur frontal

Pour ne pas saisir l’URL de Jenkins avec le port 8080, on peut positionner un serveur web frontal devant le serveur Jenkins. Pour cela, il faut configurer un mode reverse-proxy pour permettre l’accès au serveur Jenkins via le serveur web frontal (Apache2 ou Nginx).

### Reverse-proxy avec un serveur Apache2 frontal

Pour installer un serveur Apache2 frontal :

  1. Créer un virtualhost pour  `Jenkins`

    Créer un virtualhost Jenkins dont le fichier de configuration `jenkins.conf` sera dans le répertoire `/etc/apache2/sites-available/`. Copier dans ce fichier le contenu ci-dessous. Dans cette configuration, on considérera que l’URL d’accès au frontal est `http://jenkins.test.com/` (à modifier dans votre configuration).

    ```shell
    <VirtualHost *:80>
      ServerName http://jenkins.test.com/
      RewriteEngine On
      LogLevel alert rewrite:trace2
      LogFormat "%h %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-agent}i\"" combined
      CustomLog "|/usr/bin/rotatelogs /var/log/apache2/rewrite_jenkins_%Y%m%d.log 86400" combined

      RewriteRule ^/$ http://127.0.0.1:8080/ [P]
      RewriteRule ^/(.*)$ http://127.0.0.1:8080/$1 [P]
      ProxyPassReverse / http://127.0.0.1:8080/
    </VirtualHost>
    ```

  2. Reload de la configuration d'Apache2

    ```
    root@vm-debian-1:~# systemctl reload apache2.service
    ```

  3. Modification de la configuration de Jenkins

    Puisque Jenkins sera accessible maintenant via un reverse-proxy, il faut empêcher l’accès à Jenkins via son URL avec le port 8080. Jenkins doit écoute uniquement les connexions provenant d'Apache2. Modifier le fichier `/etc/default/jenkins` et la variable `JENKINS_ARGS` en y ajoutant `--httpListenAddress=127.0.0.1`

    ```
    JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --httpListenAddress=127.0.0.1"
    ```

    Redémarrer Jenkins pour la prise en compte de la nouvelle configuration.

    ```
    root@vm-debian-1:~# systemctl restart jenkins.service
    ```

### Reverse-proxy avec un serveur Nginx frontal

Pour installer un serveur Nginx frontal :

  1. Créer un virtualhost pour  `Jenkins`

    Créer un virtualhost Jenkins dont le fichier de configuration `jenkins.conf` sera dans le répertoire `/etc/nginx/sites-available/`. Copier dans ce fichier le contenu ci-dessous. Dans cette configuration, on considérera que l’URL d’accès au frontal est `http://enterprise.jenkins.com/` (à modifier dans votre configuration).

    ```shell
      server {
          server_name jenkins.syscom.com;
          access_log /var/log/nginx/jenkins.access.log;
          error_log /var/log/nginx/jenkins.error.log;
          location / {
                  include /etc/nginx/proxy_params;
                  proxy_pass http://localhost:8080;
                  proxy_read_timeout 90s;
                }
      }
    ```

  2. Activer le virtualhost en créant un lien vers les sites actifs.

    ```
    root@vm-debian-1:~# ln -s /etc/nginx/sites-available/jenkins /etc/nginx/sites-enabled/jenkins
    root@vm-debian-1:~# ll /etc/nginx/sites-enabled/
    total 0
    lrwxrwxrwx 1 root root 34 oct.  11 17:31 default -> /etc/nginx/sites-available/default
    lrwxrwxrwx 1 root root 34 oct.  12 13:47 jenkins -> /etc/nginx/sites-available/Jenkins
    ```

  3. Vérifier que la syntaxe de la nouvelle configuration est correcte et reload de la coniguration.

    ```
    root@vm-debian-1:~# nginx -t   
    nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
    nginx: configuration file /etc/nginx/nginx.conf test is successful
    root@vm-debian-1:~# systemctl reload nginx.service
    ```

  4. Modification de la configuration de Jenkins

     Comme pour le serveur Apache2, il faut que Jenkins écoute uniquement les connexions provenant de Nginx. Modifier le fichier `/etc/default/jenkins` et la variable `JENKINS_ARGS` en y ajoutant `--httpListenAddress=127.0.0.1`

      ```
      JENKINS_ARGS="--webroot=/var/cache/$NAME/war --httpPort=$HTTP_PORT --httpListenAddress=127.0.0.1"
      ```

      Ensuite, redémarrer Jenkins.

      ```
      root@vm-debian-1:~# systemctl restart jenkins.service
      ```

Si la configuration est bien réalisée, le serveur Jenkins sera accessible sur l’URL `http://enterprise.jenkins.com/` et on peut s’en rendre compte en regardant le fichier log `/var/log/nginx/jenkins.access.log` :

```
root@vm-debian-1:~# tailf /var/log/nginx/jenkins.access.log
192.168.56.1 - - [12/Oct/2018:13:53:22 +0200] "GET / HTTP/1.1" 403 336 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
192.168.56.1 - - [12/Oct/2018:13:53:23 +0200] "GET /login?from=%2F HTTP/1.1" 200 837 "http://enterprise.jenkins.com/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
192.168.56.1 - - [12/Oct/2018:13:53:23 +0200] "GET /static/c6e6d71e/css/simple-page-forms.css HTTP/1.1" 200 568 "http://enterprise.jenkins.com/login?from=%2F" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
192.168.56.1 - - [12/Oct/2018:13:53:23 +0200] "GET /static/c6e6d71e/css/simple-page.theme.css HTTP/1.1" 200 1021 "http://enterprise.jenkins.com/login?from=%2F" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
192.168.56.1 - - [12/Oct/2018:13:53:23 +0200] "GET /static/c6e6d71e/css/simple-page.css HTTP/1.1" 200 1327 "http://enterprise.jenkins.com/login?from=%2F" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
192.168.56.1 - - [12/Oct/2018:13:53:23 +0200] "GET /static/c6e6d71e/images/jenkins.svg HTTP/1.1" 200 16255 "http://enterprise.jenkins.com/static/c6e6d71e/css/simple-page.theme.css" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/69.0.3497.100 Safari/537.36"
```
