# TP1 : Containers



# I. Docker

## 1. Install

🌞 **Installer Docker sur la machine**
```
   34  sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
   35  sudo dnf install docker-ce docker-ce-cli containerd.io
   36  sudo systemctl start docker
   37  sudo usermod -aG docker $(whoami)
   38  sudo exit
   39  sudo logout
   40  sudo rebooot
   41  sudo reboot
```
## 2. Vérifier l'install

➜ **Vérifiez que Docker est actif est disponible en essayant quelques commandes usuelles :**

```bash
   48  docker ps
   49  docker ps -a
   50  sestatus
   51  sudo systemctl start docker
   52  docker ps
   53  docker ps -a
   54  docker run ed2bbb1ac067
   55  docker login ed2bbb1ac067
   56  sudo usermod -aG docker $(whoami)
   57  docker run ed2bbb1ac067
   58  sudo usermod -aG docker $(whoami)
   59  ls -l /var/run/docker.sock
   60  sudo systemctl restart docker
   61  docker ps
   62  docker ps -a
   63  docker run tp1docker1
   64  sudo docker run tp1docker1
```




🌞 **Utiliser la commande `docker run`**

```bash
   98  docker run -p 8888:80 mon_nginx
```

```
[rocky@node1tp1cloud tp_cloud]$ cat index.html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Bonjour</title>
</head>
<body>
    <h1>Bonjour, <span id="prenom">Prénom</span>!</h1>
    <script>
        // Remplace "Prénom" par le prénom que tu veux afficher
        document.getElementById("prenom").innerText = "Theodore";
    </script>
</body>
</html>
```

```
[rocky@node1tp1cloud tp_cloud]$ cat tp1.conf
server {
  # on définit le port où NGINX écoute dans le conteneur
  listen 9999;

  # on définit le chemin vers la racine web
  # dans ce dossier doit se trouver un fichier index.html
  root /var/www/tp_docker;
}
```



# II. Images

```
[rocky@node1tp1cloud tp_cloud]$ cat Dockerfile
# Utilise l'image Debian comme base
FROM debian:latest

# Met à jour le système
RUN apt-get update -y

# Installe Apache
RUN apt-get install -y apache2

# Crée le répertoire pour les logs
RUN mkdir -p /etc/apache2/logs

# Copie le fichier de configuration Apache personnalisé
COPY apache2.conf /etc/apache2/apache2.conf

# Crée le répertoire pour le site web
RUN mkdir -p /var/www/html

# Copie la page d'accueil HTML personnalisée
COPY index.html /var/www/html/

# Expose le port 80
EXPOSE 80

# Commande pour lancer Apache en mode foreground
CMD ["apache2", "-D", "FOREGROUND"]

[rocky@node1tp1cloud tp_cloud]$

```




## 2. Construisez votre propre Dockerfile

🌞 **Construire votre propre image**

```
 docker build . -t mon_image_apache
 docker run -p 8080:80 mon_image_apache
```
```
  137  nano apache2.conf
```
```apache2
# on définit un port sur lequel écouter
Listen 80

# on charge certains modules Apache strictement nécessaires à son bon fonctionnement
LoadModule mpm_event_module "/usr/lib/apache2/modules/mod_mpm_event.so"
LoadModule dir_module "/usr/lib/apache2/modules/mod_dir.so"
LoadModule authz_core_module "/usr/lib/apache2/modules/mod_authz_core.so"

# on indique le nom du fichier HTML à charger par défaut
DirectoryIndex index.html
# on indique le chemin où se trouve notre site
DocumentRoot "/var/www/html/"

# quelques paramètres pour les logs
ErrorLog "logs/error.log"
LogLevel warn
```


📁 **`Dockerfile`**

# III. `docker-compose`

## 1. Intro

```
 177  nano docker-compose.yml
```
```
[rocky@node1tp1cloud compose]$ cat docker-compose.yml
version: "3"
services:

  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_DB: wiki
      POSTGRES_PASSWORD: wikijsrocks
      POSTGRES_USER: wikijs
    logging:
      driver: "none"
    restart: unless-stopped
    volumes:
      - db-data:/var/lib/postgresql/data

  wiki:
    image: ghcr.io/requarks/wiki:2
    depends_on:
      - db
    environment:
      DB_TYPE: postgres
      DB_HOST: db
      DB_PORT: 5432
      DB_USER: wikijs
      DB_PASS: wikijsrocks
      DB_NAME: wiki
    restart: unless-stopped
    ports:
      - "80:3000"

volumes:
  db-data:
```

## 2. WikiJS

WikiJS est une application web plutôt cool qui comme son nom l'indique permet d'héberger un ou plusieurs wikis. Même principe qu'un MediaWiki donc (solution opensource utilisée par Wikipedia par exemple) mais avec un look plus moderne.

🌞 **Installez un WikiJS** en utilisant Docker

````
  183  docker pull requarks/wiki
  184  docker pull postgres
  185  sudo docker compose up -d
  186  i a
````
