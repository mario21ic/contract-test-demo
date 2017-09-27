Dockerised Pact Broker [![Build Status](https://travis-ci.org/DiUS/pact_broker-docker.svg)](https://travis-ci.org/DiUS/pact_broker-docker)
==================

This repository deploys [Pact Broker](https://github.com/pact-foundation/pact_broker) using lightweight containers using Docker. You can pull the dius/pact-broker image from [Dockerhub](https://hub.docker.com/r/dius/pact-broker/).

## Prerequisites

* A running postgresql database and the ability to connect to it (see [POSTGRESQL.md](POSTGRESQL.md)).
* If on Mac, you will need the `timeout` or `gtimeout` function. You can install `gtimeout` using `brew install coreutils`.

## Getting Started

1. [Install Docker](https://docs.docker.com/engine/installation/)
2. Prepare your environment if you are not running postgresql in a docker container. Setup the pact broker connection to the database through the use of the following environment variables. If you want to use a disposable postgres docker container just do `export DISPOSABLE_PSQL=true` before running the [script/test.sh](script/test.sh).

For a postgres or mysql database:

    * PACT_BROKER_DATABASE_ADAPTER (optional, defaults to 'postgres', see note below.)
    * PACT_BROKER_DATABASE_USERNAME
    * PACT_BROKER_DATABASE_PASSWORD
    * PACT_BROKER_DATABASE_HOST
    * PACT_BROKER_DATABASE_NAME

Adapter can be 'postgres' (recommended) or 'mysql2' (please note that future JSON search features may not be supported on mysql).

For an sqlite database (only recommended for investigation/spikes, as it will be disposed of with the container unless you mount it from an external file system):

  * PACT_BROKER_DATABASE_ADAPTER (set to 'sqlite')
  * PACT_BROKER_DATABASE_NAME (arbitrary name eg. pact_broker.sqlite)

3. Test the pact broker environment by executing [script/test.sh](script/test.sh)

## Notes

* Use `-p 80:80` to start the docker image, as some of the Rack middleware gets confused by receiving requests for other ports and will return a 404 otherwise (port forwarding does not rewrite headers).
* On OSX, if you are not using Docker native, use `docker-machine ip $(docker-machine active)` to get the IP of the VirtualBox, and connect on port 80.
* The application makes use of the phusion passenger application server.
* Apart from creating a database no further preparation is required.

## Using basic auth
Run your container with `PACT_BROKER_BASIC_AUTH_USERNAME` and `PACT_BROKER_BASIC_AUTH_PASSWORD` set to enable basic auth for the pact broker application.

## Running with Docker Compose

For a quick start with the Pact Broker and Postgres, we have an example
[Docker Compose](docker-compose.yml) setup you can use:

1. Modify the `docker-compose.yml` file as required.
2. Run `docker-compose up` to get a running Pact Broker and a clean Postgres database

Now you can access your local broker:

```sh
# Get IP of your running Docker instance
DOCKER_HOST=$(docker-machine ip $(docker-machine active))
curl -v http://$DOCKER_HOST # you can visit in your browser too!
```

_NOTE: this image should be modified before using in Production, in particular, the use of hard-coded credentials_
