FROM debian:stretch-slim
#ENTRYPOINT [ "/docker-entrypoint" ]
CMD ["apache2", "-D", "FOREGROUND"]

ENV BASE_PATH="" \
	SHIB_HANDLER_URL=/Shibboleth.sso \
	APACHE_RUN_DIR=/var/run/apache2 \
	APACHE_RUN_USER=www-data \
	APACHE_RUN_GROUP=www-data

#ADD assets/docker-entrypoint /docker-entrypoint
ADD assets/*.conf /etc/apache2/conf-available/

#RUN chmod a+x /docker-entrypoint \
RUN apt-get update \
	&& apt-get install -y --no-install-recommends libapache2-mod-shib2 openssl apache2 \
	&& openssl genrsa -out /etc/ssl/private/ssl-cert-snakeoil.key 2048 \
	&& openssl req -nodes -new -x509 -newkey rsa:4096 -subj "/CN=localhost" -keyout /etc/ssl/private/ssl-cert-snakeoil.key -out /etc/ssl/certs/ssl-cert-snakeoil.pem -days 3650 \
	&& apt-get purge -y openssl \
	&& apt-get autoremove -y --purge \
	&& sed -e 's!^\(ErrorLog\).*$!\1 /proc/self/fd/2!' -i /etc/apache2/apache2.conf \
	&& sed -e 's!^\(PidFile.*\)$!#\1!' -i /etc/apache2/apache2.conf \
	&& sed -e 's!^\(\s*ErrorLog\).*$!\1 /proc/self/fd/2!' -i /etc/apache2/sites-available/000-default.conf \
	&& sed -e 's!^\(\s*CustomLog\).*$!\1 /proc/self/fd/1 combined!' -i /etc/apache2/sites-available/000-default.conf \
	&& sed -e 's!^\(\s*ErrorLog\).*$!\1 /proc/self/fd/2!' -i /etc/apache2/sites-available/default-ssl.conf \
	&& sed -e 's!^\(\s*CustomLog\).*$!\1 /proc/self/fd/1 combined!' -i /etc/apache2/sites-available/default-ssl.conf \
  && a2disconf serve-cgi-bin other-vhosts-access-log \
	&& a2dismod shib2 \
	&& a2enmod rewrite proxy_fcgi \
	&& a2ensite default-ssl \
	&& a2enconf debug ssl shibboleth \
	&& rm -r /var/lib/apt/lists/*