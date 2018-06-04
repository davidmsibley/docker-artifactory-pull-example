## Pulling From Artifactory As Part Of A Travis Build

[![Build Status](https://travis-ci.org/USGS-CIDA/docker-artifactory-pull-example.svg?branch=master)](https://travis-ci.org/USGS-CIDA/docker-artifactory-pull-example)

The Docker engine requires that a Docker repository be served from the root context.
Externally, Artifactory is set up to serve at https://cida.usgs.gov/artifactory/.
Worse still, accessing the Artifactory Docker repositories requires adding depth
to the application context already not at root. For example, to access the ["owi-docker"
Artfactory repository](https://cida.usgs.gov/artifactory/webapp/#/artifacts/browse/tree/General/owi-docker), the context depth looks like `https://cida.usgs.gov/artifactory/api/docker/owi-docker/v2/...`

The Docker engine accesses registries at the root context using `/v2/` as the begining context.  We can have Travis run an NGINX container that acts as a reverse proxy for the local host. The proxy takes any calls to `https://localhost/v2/...` and calls  `https://cida.usgs.gov/artifactory/api/docker/owi-docker/v2/...` on the back-end.

Artifactory, when using the `/artifactory/api/` path also required authentication. The Docker repositories are open for all to view so the `anonymous` user is used in the NGINX header auth block to the Artifactory server. This allows the Docker engine to transparently communicate to Artifactory without requiring any authentication.

The Travis configuration also creates wildcard certs that NGINX uses so that the Docker engine doesn't have issues with the SSL communication between itself and NGINX.

Once NGINX is running, the Travis configuration pulls down `water_auth_server:latest` image from Artifactory (as an example). Once pulled down, I then tag the image to match the internal naming scheme in Artifactory. The scheme is what's used by internal applications to pull from Artifactory.

I then build a derivative image from this example base image and verify that the base image is functioning.

[Questions?](mailto:isuftin@usgs.gov)  
