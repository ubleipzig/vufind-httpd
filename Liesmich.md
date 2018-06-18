# vufind-httpd

*vufind-httpd* ist der Standard-Webserver von [VuFind], welcher Datei-Requests ausliefert und Anfragen an den PHP-Service weiterleitet. Das Basis-Image ist [httpd:alpine].

Das Image wurde dahingehend erweitert, dass eine Standard-Konfiguration eingefügt wird, welche die [VuFind]-spezifische Konfiguration beinhaltet. Die Konfiguration erwartet die  Quellen von [VuFind] unter `/usr/local/vufind`, die Cache-Dateien, welche von PHP/VuFind zur Laufzeit erzeugt werden, werden unter `/var/cache/vufind` erwartet.

Weiterhin wurde ein eigener *entrypoint* erstellt, welcher den Pfadanteil der Request-URL anhand der Umgebungsvariablen `BASE_PATH` anpasst. Möchte man [VuFind] unter http://localhost/vufind aufrufen, muss man bei Container-Start die Umgebungsvariable `BASE_PATH=/vufind` mitgeben.

## Unterstützte tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])
* vufind1-2.4-*, vufind1-2.4, vufind1-2, vufind1 ([vufind1/Dockerfile])

## Nutzung des Images

Die Nutzung des Images macht nur Sinn im Zusammenhang mit [vufind-php], welches den Applikationsserver von [VuFind] zur Verfügung stellt. Der Applikationsserver muss als Host *php* erreichbar sein. Weiterhin muss der Ordner mit den [VuFind]-Dateien in den Container gebunden werden, damit der Webserver statische Inhalte ausliefern kann. [VuFind] erzeugt zusätzlich Cache-Dateien, die vom Webserver ausgeliefert werden. Dieser Ordner muss ebenfalls an den Container gebunden werden.

Man kann den Webserver wie folgt starten:
```bash
$# docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --environment BASE_PATH=/vufind \
  ubleipzig/vufind-httpd
```

## erweiterte Konfiguration

### SSL

Um den Container mit SSL-Unterstützung zu starten, muss der Startbefehl angepasst werden:

    httpd -D FOREGROUND -D ssl

Dadurch werden in Apache2 erforderiche Module und Konfigurationen geladen, um SSL an Port 443 zur Verfügung zu stellen. Als Schlüssel und Zertifikate werden im Image eingebaute selbstignierte Objekte benutzt. Möchte man eigens erstellte Objekte nutzen, so müssen die Dateien `/usr/local/apache2/conf/server.key` und `/usr/local/apache2/conf/server.crt` entsprechend überschrieben werden. z.B.

```bash
$# docker run --name httpd \
  --link php:php \
  --volume /path/to/vufind:/usr/local/vufind:ro \
  --volume /path/to/cache:/var/cache/vufind:ro \
  --volume /path/to/ssl_key.pem:/usr/local/apache2/conf/server.key:ro \
  --volume /path/to/ssl_cert.pem:/usr/local/apache2/conf/server.crt \
  --environment BASE_PATH=/vufind \
  ubleipzig/vufind-httpd \
  httpd -D FOREGROUND -D ssl
```

## Anmerkungen

* Das *vufind1*-Image existiert, um Entwicklern das Umschalten zu VuFind1-Instanzen so einfach wie möglich zu machen. Hier wird keine weitere Arbeit investiert, z.B. funktioniert die Pfad-Konfiguration über die `BASE_PATH`-Variable nicht.
* es müssen Tests geschrieben werden, sobald ich weiß, wie man das für Docker-Images am besten macht

[VuFind]: https://github.com/vufind-org/vufind
[httpd:alpine]: https://hub.docker.com/_/httpd/
[2.4/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/2.4/Dockerfile
[vufind1/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/vufind-httpd/blob/master/vufind1/Dockerfile
[vufind-php]: https://hub.docker.com/r/ubleipzig/vufind-php/
