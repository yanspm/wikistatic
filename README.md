wikistatic basé sur jekyll
==========

![Alt text](images/notepad-with-pencil-128x128.png?raw=true "wikistatic")

[jekyll](https://jekyllrb.com/) est un générateur de blog statique avec pour avantages , un accès au site très performant , sécurité excellente (que des pages statiques html) ,écriture des billets syntaxe markdown

## Caractéristiques

- Disposition "responsive".
- Supporte les tags et les catégories.
- Profil social et bio de l'auteur.
- Basé sur Bootstrap ,jekyll et thème dbyll.
- Icônes Glyphicon et Font-Awesome.
- Pagination.
- Syntaxe Kramdown.
- Recherche

## Prérequis


Les procédures sont applicables sur un serveur debian jessie

```bash
sudo apt update
sudo apt upgrade
```

Ensuite, vous devez installer Ruby , les dépendances et le nécessaire pour la compilation des modules ( Jekyll est écrit en Ruby ) :

```bash
sudo apt install ruby-full build-essential ruby-rmagick zlib1g-dev liblzma-dev 
```

Une fois le processus terminé, vous pouvez vérifier que Ruby est installé avec succès en utilisant la commande suivante:

```bash
  ruby -v
     ruby 2.1.5p273 (2014-11-13) [x86_64-linux-gnu]
```


## Installation

Ce **wikistatic** basé sur  **Jekyll** utilise le thème libre [dbyll](https://github.com/dbtek/dbyll)  

Création dossier

    sudo mkdir -p /srv     # création dossier

Les droits sur le dossier

    sudo chown   $USER.users -R /srv/


Deux options d'installation , git clone ou fichier ZIP

A-Clonage wikistatic par git

```bash
sudo apt install git 
cd /srv
git clone https://gitlab.xeuyakzas.xyz/spm/wikistatic.git
```

B-Téléchargement zip

```bash
cd /srv
wget https://gitlab.ouestline.net/open-source/wikistatic/repository/archive.zip?ref=master -O wikistatic.zip
unzip master.zip
```

Suite installation...

```bash
cd wikistatic
sudo gem install bundler  
bundle install
```

Patienter quelques minutes...

```bash
Fetching gem metadata from https://rubygems.org/............
Fetching version metadata from https://rubygems.org/...
Fetching dependency metadata from https://rubygems.org/..
Installing colorator 0.1
[...]
Installing jekyll 3.1.3
Installing jekyll-srcset 0.1.3
Bundle complete! 5 Gemfile dependencies, 25 gems now installed.
Use `bundle show [gemname]` to see where a bundled gem is installed.
```

## Exécution pour test

On lance manuellement le serveur pour un test,choisir l'une des 2 méthodes

### méthode A

Exécution , **lancement serveur pour test**

```bash
  bundle exec jekyll serve
```

```
[...]
 Auto-regeneration: enabled for '/srv/wikistatic'
Configuration file: /srv/wikistatic/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.

```


### méthode B

#### Construction  

```bash
  jekyll build
```

```
WARN: Unresolved specs during Gem::Specification.reset:
      rouge (~> 1.7)
      jekyll-watch (~> 1.1)
WARN: Clearing out unresolved specs.
Please report a bug if this causes problems.
Configuration file: /srv/jekyll/dbyll/_config.yml
            Source: /srv/jekyll/dbyll
       Destination: /srv/jekyll/dbyll/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 2.428 seconds.
 Auto-regeneration: disabled. Use --watch to enable.
```

#### Exécution  

```bash
  jekyll serve --watch
```

```bash
WARN: Unresolved specs during Gem::Specification.reset:
      rouge (~> 1.7)
      jekyll-watch (~> 1.1)
WARN: Clearing out unresolved specs.
Please report a bug if this causes problems.
Configuration file: /srv/jekyll/dbyll/_config.yml
            Source: /srv/jekyll/dbyll
       Destination: /srv/jekyll/dbyll/_site
 Incremental build: disabled. Enable with --incremental
      Generating... 
                    done in 0.317 seconds.
 Auto-regeneration: enabled for '/srv/jekyll/dbyll'
Configuration file: /srv/jekyll/dbyll/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

### Le lien serveur test

Le serveur est en local sur le port 4000 (<http://localhost:4000>) , on l'arrête par *Ctrl c*


## Serveur de production

Sur un serveur debian jessie avec **nginx** installé, accès par proxy au serveur **jekyll**  
On utilise un service **systemd** pour lancer automatiquement le serveur **jekyll**

### Proxy Jekyll/Nginx

Exemple avec un site <http://site.statique.tld>  
Utilisation **proxy** sur nginx pour accèder au serveur local **Jekyll** 

Créer une configuration nginx soud **/etc/nginx/conf.d**


```nginx
server {
    listen 80;

    server_name site.statique.tld;

	location / {
	    proxy_pass http://127.0.0.1:4000;
	}
    access_log /var/log/nginx/static-access.log;
    error_log /var/log/nginx/static-error.log;
}
```

Relancer le service nginx

```bash
sudo systemctl restart nginx
```

## Jekyll/Systemd

Pour lancer le serveur Jekyll au démarrage , créer un bash sous /srv

    sudo nano /srv/wikistatic/start_jekyll.sh

```
#!/bin/sh
#lancement jekyll
cd /srv/wikistatic/
bundle exec jekyll serve
```

Droits sur le bash

```
sudo chown $USER.users /srv/wikistatic/start_jekyll.sh
chmod +x /srv/wikistatic/start_jekyll.sh
```

Pour lancer le serveur **Jekyll** au démarrage, utilisation d'un <u>service systemd</u>  
**ATTENTION!** , remplacer *utilisateur* par votre nom d'utilisateur (**echo $USER**)

    sudo nano /etc/systemd/system/jekyll.service

Contenu du fichier

```ini
[Unit]
Description=jekyll Service
After=network.target

[Service]
Type=simple
User=utilisateur
ExecStart=/bin/sh /srv/wikistatic/start_jekyll.sh
Restart=on-abort


[Install]
WantedBy=multi-user.target
```

Lancer le service  **jekyll** :

```bash
sudo systemctl daemon-reload
sudo systemctl start jekyll
  #Vérifier:
sudo systemctl status jekyll
```


```
● jekyll.service - jekyll Service
   Loaded: loaded (/etc/systemd/system/jekyll.service; disabled)
   Active: active (running) since dim. 2017-01-08 21:02:34 CET; 13s ago

[...]
janv. 08 21:02:35 jessie sh[30873]: Auto-regeneration: enabled for '/srv/wikistatic'
janv. 08 21:02:35 jessie sh[30873]: Configuration file: /srv/wikistatic/_config.yml
janv. 08 21:02:35 jessie sh[30873]: Server address: http://127.0.0.1:4000/
janv. 08 21:02:35 jessie sh[30873]: Server running... press ctrl-c to stop.
```

Vérifier l'accès au site static  <http://site.statique.tld>

Valider le lancement du **service jekyll au démarrage**

```bash
sudo systemctl enable jekyll
```

## Rédaction des billets

* syntaxe markdown    
* le nom du fichier débute obligatoirement par la date au format *aaaa-mm-jj** , suffixe **.md**
* Ni espace , ni accents et ni caractères spéciaux dans le nom (exemple : 2106-04-21-synyaxe-ecriture-fichier.md)
* Entête du fichier (description):  

```
---
layout: post
title:  "Titre du billet qui s'affichera dans la liste"
toc : false
ref: (falcutatif)
date: 2016-04-20
categories: [rubrique_catégorie]
tags: [rubrique_mots_clés]
lang: fr
description: Brève description
---
```

**ATTENTION !** , respecter le format de la date **aaaa-mm-jj** , si plusieurs catégories et tags , ils sont séparés par une 'virgule'   
le code qui contient des doubles accolades {% raw %}{{ }}{% endraw %} doit être précédé de **raw** et se terminer par **endraw** ![raw endraw]({{ site.url }}/images/raw-endraw.png) 

Lorsque le fichier **.md** est déposé dans le dossier **_posts** , il est validé automatiquement par jekyll 


Le plugin [Jekyll Srcset](https://github.com/netlify/jekyll-srcset) rend très facile le fait d'envoyer des images plus grandes à des appareils à forte densité de pixels.  
Le plugin ajoute une balise Liquid `image_tag` qui peut être utilisé comme ceci :  

{% image_tag src="/images/srcset1.png" width="300" %}

Vous devez spécifier un `width` ou` height`, mais jamais les deux. La largeur ou la hauteur seront utilisés pour déterminer la plus petite version de l'image à utiliser (pour les appareils de densité de pixel 1x). Sur la base de cette taille minimale, le plugin va générer jusqu'à 3 versions de l'image, celle qui correspond à la dimension spécifiée, celle qui est deux fois la taille et celle qui est 3 fois la taille.  
Le plugin définit ces derniers comme un attribut **srcset** sur l'étiquette de l'image finale, et les navigateurs modernes utiliseront cette information pour déterminer quelle version de l'image est à charger en fonction de la densité de pixel du dispositif.  
Pour utiliser des variables pour l'image ou les dimensions ,définition entre les balises :  
`image_tag src=page.cover_image height=page.cover_image_height`  


Le plugin [jekyll-toc](https://github.com/toshimaru/jekyll-toc) génère une table des matières.  
Ecrire `toc: true` dans vos publications (posts).

```yml
---
layout: post
title: "Welcome to Jekyll!"
toc: true
---
```

