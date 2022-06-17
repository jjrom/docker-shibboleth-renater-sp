FROM httpd:2.4.54

# apache2: to have the apache web server able to authenticate and to reverse proxy the real application
# libapache2-mod-shib: to have mod_shib for apache and the shibd daemon
# Apache and mod_shib versions are fixed to be sure 
# nothing moves between two docker build.
# To get the latest stable, this quick search can be done:
# https://packages.debian.org/search?keywords=apache2&searchon=names&suite=stable&section=all
# https://packages.debian.org/search?keywords=libapache2-mod-shib&searchon=names&suite=stable&section=all
RUN apt-get update \
  && apt-get -y install apache2=2.4.53-1~deb11u1 libapache2-mod-shib=3.2.2+dfsg1-1 \
  && apt-get clean \
  && apt-get -y autoremove

# ssl: for apache beeing able to listen with HTTPS (SGY 10/06/2022: WHY is it mandatory to have HTTPS ?)
# shib: the famouse apache mod for beeing able to authenticate users thanks to shibboleth and SAML
# proxy_http: for apache beeing able to proxy user's HTTP requests to the real application
RUN sed -i \
  -e 's/^#\(LoadModule .*mod_ssl.so\)/\1/' \
  -e 's/^#\(LoadModule .*mod_socache_shmcb.so\)/\1/' \
  -e '/LoadModule proxy_module/s/^#//g' \
  -e '/LoadModule proxy_http_module/s/^#//g' \
  -e '/LoadModule rewrite_module/s/^#//g' \
  -e '/LoadModule rewrite_module/a LoadModule mod_shib /usr/lib/apache2/modules/mod_shib.so' \
  -e '/httpd-vhosts.conf/s/^#//g' \
  /usr/local/apache2/conf/httpd.conf

# apache conf and shib config
# (templates in order to be able to customize it fro external parameters)
COPY ./httpd-vhosts.conf /usr/local/apache2/conf/extra/httpd-vhosts.conf
COPY ./shibboleth/ /etc/shibboleth/

# redirect apache logs to docker stdout/stderr
RUN ln -sf /proc/1/fd/1 /var/log/apache2/access.log
RUN ln -sf /proc/1/fd/2 /var/log/apache2/error.log

# creates the entrypoint of this container
COPY ./httpd-foreground /usr/local/bin/
CMD [ "httpd-foreground" ]

EXPOSE 443