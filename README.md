# docker-shibboleth-renater-sp

Ce dépôt propose une image docker générique permettant de mettre en place un fournisseur de service connecté sur la [Fédération d'identités Education-Recherche (FER)](https://services.renater.fr/federation/index).

Fonctionnement : ce fournisseur de service s'intégre sur une application tiers en se positionnant en amon des flux HTTP et en jouant le rôle de reverse proxy authentifiant. C'est à dire qu'une requête HTTP permettant d'accéder à l'application va tout d'abord transiter par cette brique pour ensuite être transmise à l'application cible. L'avantage de cette approche c'est de pouvoir intégrer une authentification SAML sur une application sans avoir à la modifier en profondeur, c'est ce reverse proxy qui se charge de transmettre une preuve de l'authentification à l'application. Cette preuve contient l'identifiant de l'utilisateur ainsi que les attributs fournits par le fournisseur d'identités (ex: mail, nom, prénom, supannEtablissement, etc ...).

Technologies : cette image docker utilise un serveur apache (basée sur son image docker officielle) et mod_shib (module Shibboleth s'intégrant dans apache). Ce sont les briques techniques préconisées par RENATER pour implémenter un fournisseur de service.

## Comment construire cette image docker ?

Cette image est construite et publiée automatiquement sur dockerhub à l'aide de github action: TODO

Mais il est également possible de la construire localement avec cette commande (utile pour le développement de cette image) :
```bash
cd docker-shibboleth-sp/
docker-compose build
```

## Comment utiliser cette image ?

## Configuration

1) Vous avez besoin de générer un couple de clé SSH privée/publique dédié pour le démon shibboleth qui tournera dans le conteneur. Ces clés sont ici dans l'image : ``/etc/shibboleth/ssl/server.key`` et ``/etc/shibboleth/ssl/server.crt``. Remarque : la clé ``server.key`` (privée) is critique et ne doit jamais être partagée mais pour des raison de démonstration, un couple de clé ``server.key`` et ``server.crt`` est inclu dans cette image ce qui permet de tester facilement cette image sans avoir à en générer une soit même. En revanche, cette clé ne doit **jamais être utilisée en production** car elle est [accessible publiquement dans le code source ici](https://github.com/abes-esr/docker-shibboleth-renater-sp/blob/main/image/shibboleth/ssl/server.key). Pour votre passage en production, nous aurez besoin de générer un couple de clé vous même. Voici les lignes de commandes permettant de générer un couple de clé auto-signées avec un long délai d'expiration (7300 jours = 20 ans!) comme recommandé par [RENATER](https://services.renater.fr/federation/documentation/generale/certificats-saml#recommandations_techniques_pour_les_certificats) :
   ```
   cd docker-shibboleth-sp/volume/shibboleth/ssl/
   openssl genrsa -out server.key 2048
   openssl req -new -key server.key -out server.csr
   openssl x509 -req -days 7300 -in server.csr -signkey server.key -out server.crt
   ```

3) Vous devez ensuite personnaliser les variables de votre fournisseur de service en copiant ``.env-dist`` dans ``.env`` et en personnalisant les valeurs:
   ```bash
   APPLI_APACHE_SERVERNAME="https://appolo-dev.theses.fr"
   APPLI_APACHE_SERVERADMIN="admin@example.fr"
   APPLI_APACHE_LOGLEVEL="info ssl:warn"
   ```
2) Vous devez ensuite enregistrer votre fournisseur de service dans la [fédération d'identités Education-Recherche](https://federation.renater.fr/registry?action=get_all)

3) Vous pouvez tester votre fournisseur de service en naviguant sur l'URL suivante : https://votre-ip/my-protected-url/


### Demo

Pour lancer l'image docker depuis le docker-compose exemple:
```bash
docker-compose up
```

## Voir aussi

- Image dérivée de https://github.com/BibCnrs/docker-shibboleth-sp avec un exemple de configuration ici https://github.com/BibCnrs/BibRP/
- Cette image s'inspire aussi de :
  - https://github.com/mconf/apache-shib-docker/
  - https://gitlab.oit.duke.edu/devil-ops/duke-shibboleth-container


