FROM httpd:2.4-alpine
ENTRYPOINT [ "/docker-entrypoint" ]
CMD ["httpd-foreground"]

ENV BASE_PATH=""

ADD assets/docker-entrypoint /docker-entrypoint

ADD assets/httpd.conf /usr/local/apache2/conf/httpd.conf

RUN chmod a+x /docker-entrypoint \
	&& apk add --no-cache openssl \
	&& openssl genrsa -out /usr/local/apache2/conf/server.key 2048 \
	&& openssl req -nodes -new -x509 -newkey rsa:4096 -subj "/CN=localhost" -keyout /usr/local/apache2/conf/server.key -out /usr/local/apache2/conf/server.crt -days 3650 \
	&& apk del --no-cache openssl
