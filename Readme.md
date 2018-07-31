# httpd

This repository holds the Dockerfiles and configuration for the Apache2-webserver, adjusted to serve vufind and other php-related applications developed by UBL.

Basically we added shibboleth-support and expect all application-requests to be made by proxy-fcgi. every application using this service has to bring its own proxy-fcgi configuration. Furthermore we added ssl  to support ssl-request out-of-the-box and add the debug-feature to increase ProxyTimeout, which is useful in debug-sessions.

## Image-Tags

The images are created via a gitlab-pipeline, see [.gitlab-ci.yml]. There are several tags which can be used:

* `2.4-*`: points to a specific build. Each build is specified by a number. The higher, the latter.
* `2.4`: points to the latest build from the `2.4`-line. It is the same as the last `2.4-*`.
* `2`: points to the latest build from the `2`-line. If there will be a `2.6`-line it will point to that latest build.
* `latest`: always points to the latest build.

## create Images

Pushing the Code to the Repository does nothing. Images are created via GIT-Tags.

```
git tag -a 2.4-2 -m 'minor optimization'
git push origin 2.4-2
```
_this will create a new image with a tag named `2.4-2`. Also the Tags `2.4`, `2`, and `latest` will point to this image._

Only Repository-Masters will be able to create a new Tag.

### Alpha-Images
Each branch beginning with as number as for merge requests created by gitlab results in an alpha-image named `ubleipzig/httpd:2.4-<branch-name>`. This images are built when the branch is pushed.

### Staging-Images
When a push to `master` is made gitlab-ci creates an image named `ubleipzig/httpd:2.4-staging`.

## Contribution

In case you want to contribute please fork and make a pull-request at [Gitlab-hosting of Leipzig University]. This is due to internal policies and the higher flexibility when it comes to build images and push to [Docker-Hub]

## Todo

* Tests

[.gitlab-ci.yml]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/httpd/blob/master/.gitlab-ci.yml
[Gitlab-hosting of Leipzig University]: https://git.sc.uni-leipzig.de/ubl/bdd_dev/docker/httpd
[Docker-Hub]: https://hub.docker.com/r/ubleipzig/httpd/