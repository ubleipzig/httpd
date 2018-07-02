# httpd

*httpd* is a webserver container, which delivers file-requests and passes requests through to the php-service. the image is based on [debian:stretch-slim].

The image is extended by shibboleth-support which depends on a running container based on [ubleipzig/shibboleth] though. Furthermore a self-signed certificate was added to the image to provide ssl-reguests.

## supported tags

* 2.4-*, 2.4, 2, latest ([2.4/Dockerfile])

## Usage of the image

The image can be used in connection with an application server which can be accessed via proxy-fcgi. The user is responsible for a specific configuration for that, i.e. by creating a new inheriting image, such as [ubleipzig/vufind-httpd], which uses the application server ([ubleipzig/vufind-php]) of [VuFind]

out-of-the-box the server is only delivering static content. To start the webserver do as follows:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  ubleipzig/httpd
```

Be aware that the httpd-daemon needs read-access to the files it has to serve.

## advanced startup

By providing additional start options several features can be used

### SSL

To start containers with ssl-support enabled use the following startup command:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  -p 443:443 \
  ubleipzig/httpd \
  apache2 -D FOREGROUND -D ssl
```

The files for key and certificate are located under `/etc/ssl/private/ssl-cert-snakeoil.key` and `/etc/ssl/certs/ssl-cert-snakeoil.pem`. If you want to provide a real certificate, you have to overwrite these files or create a custom configuration.

### Debug

To prevent timeouts in debug-sessions the httpd-daemon can be started as follows:

```bash
$# docker run --name httpd \
  -v /path/to/static/files:/var/www/html \
  -p 8:80 \
  ubleipzig/httpd \
  apache2 -D FOREGROUND -D debug
```

By this the directive `ProxyTimeout 3600` is set, which increases the timeout for (FCGI-)Proxy-Requests to one hour.

### Shibboleth

In order to use shibboleth we depend on a configured up and running shibboleth-authenticator - accessable at `/var/run/shibd.sock`. You can get one for example by creating a container based on the image [ubleipzig/shibboleth] and link it into the httpd-container at startup:

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
