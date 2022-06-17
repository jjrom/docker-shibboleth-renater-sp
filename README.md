# docker-shibboleth-renater-sp

This is a docker image for a generic shibboleth service provider based on an apache web server. 
This apache web server can be used as a reverse proxy in charge of authenticating users with SAML.

For French academic use case, it can be easily integrated into the [fédération d'identités Education-Recherche](https://federation.renater.fr/registry?action=get_all).


## How to build the image?

This image is auto build and published on dockerhub thanks to a github action. (TODO)

But you can also build it locally:
```bash
cd docker-shibboleth-sp/
docker-compose build
```
## How to use the image?

## Configuration


1) You have to generate a private/public key dedicated for the shibboleth SP daemon and put it into in ``ssl/server.key`` and ``ssl/server.crt`` files. Notice: the generated ``server.key`` is critical and should never be shared. You can find a self-signed demo certificate into [``volume/shibboleth/ssl/server.crt``](https://github.com/abes-esr/docker-shibboleth-sp/tree/main/volume/shibboleth/ssl) it can be freely used for tests but **never use it in prod**.  
  As recommended by [RENATER, the french federated identity operator](https://services.renater.fr/federation/documentation/generale/certificats-saml#recommandations_techniques_pour_les_certificats), here is few command line example to generate a self-signed certificate with a long (7300 days = 20 years!) expiration delay ([following this doc](http://doc.ubuntu-fr.org/tutoriel/comment_creer_un_certificat_ssl)):
   ```
   cd docker-shibboleth-sp/volume/shibboleth/ssl/
   openssl genrsa -out server.key 2048
   openssl req -new -key server.key -out server.csr
   openssl x509 -req -days 7300 -in server.csr -signkey server.key -out server.crt
   ```

2) For french use case: you have to put the public key of your service provider in the [fédération d'identités Education-Recherche](https://federation.renater.fr/registry?action=get_all) 

3) If necessary change the parameters in the ``docker-compose.yml`` or into a ``.env`` file, see example:
   ```bash
   APPLI_APACHE_SERVERNAME="https://beta.theses.fr"
   APPLI_APACHE_SERVERADMIN="admin@example.fr"
   APPLI_APACHE_LOGLEVEL="info ssl:warn"
   ```

### Demo

To run the docker image thanks to the docker-compose example:
```bash
docker-compose up
```

## See also

To use the image see example at https://github.com/BibCnrs/BibRP/  
This image is inspired by : https://github.com/mconf/apache-shib-docker/ and https://gitlab.oit.duke.edu/devil-ops/duke-shibboleth-container


