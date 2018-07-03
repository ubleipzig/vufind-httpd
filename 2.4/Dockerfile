FROM ubleipzig/httpd:2.4-1

ENV BASE_PATH=""

ADD assets/vufind.conf /etc/apache2/sites-available/vufind.conf

RUN a2ensite vufind