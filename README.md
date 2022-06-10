# docker-shibboleth-sp

A docker image for a generic shibboleth service provider based on an apache web server.

To build the docker image:
```bash
cd docker-shibboleth-sp/
docker build . -t local-shib-image
```

To run the docker image:
```bash
docker run local-shib-image
```

To use the image see example at https://github.com/BibCnrs/BibRP/