FROM php:5.6.40-apache
COPY ./sources.list /etc/apt/sources.list

# pour avoir un client SMTP dans le conteneur (msmtp)
# gettext-base: pour disposer de la commande envsubst (systeme de template)
# libldap2-dev libldb-dev: pour l'extension php LDAP
# libpng-dev : pour l'extension php GD
RUN set -x \
    && DEBIAN_FRONTEND=noninteractive \
    && apt-get update && apt-get install -y --allow-unauthenticated vim msmtp gettext-base libldap2-dev libldb-dev libpng-dev
COPY ./msmtprc.tls.tmpl /etc/
COPY ./msmtprc.notls.tmpl /etc/

# configuration de php et des extensions nécessaires
RUN docker-php-ext-install gd
RUN docker-php-ext-install mysqli
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/
RUN docker-php-ext-install ldap
RUN a2enmod rewrite expires
COPY ./php-abesstp.ini /usr/local/etc/php/conf.d/php-abesstp.ini

# copie des sources php d'abesstp
COPY /src/svp-51/drupal-6.38-p7/ /var/www/html/
RUN find /var/www/html/ -type f -exec chmod a+r {} \;
RUN find /var/www/html/ -type f -exec chmod a-wx {} \;
RUN find /var/www/html/ -type d -exec chmod a-w {} \;
RUN find /var/www/html/ -type d -exec chmod a+rx {} \;

# files/ contient les pièces jointes des tickets
VOLUME /var/www/html/sites/stp.abes.fr/files/assistance/

# add ca trust Harica certificates
COPY ./HARICA_TLS_ECC_Root_CA_2021.crt /usr/share/ca-certificates/mozilla/HARICA_TLS_ECC_Root_CA_2021.crt
COPY ./HARICA_TLS_RSA_Root_CA_2021.crt /usr/share/ca-certificates/mozilla/HARICA_TLS_RSA_Root_CA_2021.crt
ECHO "mozilla/HARICA_TLS_ECC_Root_CA_2021.crt" >> /etc/ca-certificates.conf
ECHO "mozilla/HARICA_TLS_RSA_Root_CA_2021.crt" >> /etc/ca-certificates.conf
RUN update-ca-certificates

COPY ./docker-entrypoint.overload.sh /
ENTRYPOINT ["/docker-entrypoint.overload.sh"]
CMD ["apache2-foreground"]
