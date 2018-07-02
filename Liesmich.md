# httpd

*httpd* ist der Standard-Webserver, welcher Datei-Requests ausliefert und Anfragen an den PHP-Service weiterleitet. Das Basis-Image ist [debian:stretch-slim].

Das Image wurde um Shibboleth-Support ergänzt, benötigt dazu jedoch das [ubleipzig/shibboleth]-Image. Weiterhin wurde ein selbstsigniertes Zertifikat hinzugefügt, um Zugriffe per SSL zu ermöglichen.

## Unterstützte tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])

## Nutzung des Images

Das Image kann im Zusammenhang mit einem Applikationsserver, welcher per proxy-fcgi angesprochen werden kann, genutzt werden. Die Konfiguration hierzu obliegt dem Nutzer, beispielsweise durch die Erstellung eines ableitenden Images, siehe [ubleipzig/vufind-httpd], welches den Applikationsserver ([ubleipzig/vufind-php]) von [VuFind] nutzt.

Out-of-the-Box ist der Server lediglich zum Ausliefern statischer Inhalte nutzbar. Man kann den Webserver wie folgt starten:
```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  ubleipzig/httpd
```

Dabei muss darauf geachtet werden, dass der httpd-daemon entsprechende Zugriffsrechte auf die Dateien hat.

## erweiterte Startoptionen

Mithilfe erweiterter Startoptionen lassen sich zusätzliche Fähigkeiten nutzen

### SSL

Um den Zugriff via SSL zu ermöglichen, muss der httpd-Daemon mit wiefolgt gestartet werden:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  -p 443:443 \
  ubleipzig/httpd \
  apache2 -D FOREGROUND -D ssl
```

Die SSL-Dateien liegen unter `/etc/ssl/private/ssl-cert-snakeoil.key` und `/etc/ssl/certs/ssl-cert-snakeoil.pem`. Möchte man ein echtes Zertifikat verwenden, so muss man entweder diese Dateien überschreiben oder eine eigene Konfiguration einfügen.

### Debug

Um Timeouts beim Debuggen zu vermeiden, kann der httpd-Daemon wiefolgt gestartet werden:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  ubleipzig/httpd \
  apache2 -D FOREGROUND -D debug
```

Dadurch wird die Direktive `ProxyTimeout 3600` gesetzt, welche das Timeout für FCGI-Proxy-Requests auf eine Stunde setzt.

### Shibboleth

Um Shibboleth-Support nutzen zu können, wird ein konfigurierter Shibboleth-Authenticator - erreichbar an `/var/run/shibd.sock` - erwartet. Diesen erhält man beispielsweise, in dem man einen Container basierend auf dem [ubleipzig/shibboleth]-Image startet und beim Start des httpd-Container in den Container verlinkt:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  --volumes-from shibboleth \
  ubleipzig/httpd \
  apache2 -D FOREGROUND -D shibboleth
```

[VuFind]: https://github.com/vufind-org/vufind
[ubleipzig/shibboleth]: https://hub.docker.com/r/ubleipzig/vufind-shibboleth/
[ubleipzig/vufind-php]: https://hub.docker.com/r/ubleipzig/vufind-php/
[ubleipzig/vufind-httpd]: https://hub.docker.com/r/ubleipzig/vufind-httpd/
[debian:stretch-slim]: https://hub.docker.com/_/debian/
[2.4/Dockerfile]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/httpd/blob/master/2.4/Dockerfile
