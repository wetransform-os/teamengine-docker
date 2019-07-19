# Running OGC TEAM Engine with selected executable test suites on Docker

This project provides a Docker image build based on Gradle for building Docker Images with OGC TEAM Engine and specific executable test suites pre-installed.

## Build variants

Currently, there are the following build variants that define which tests in which versions are included, an which version of the TEAM Engine is used:

| Module name | Description |
| --- | --- |
| production | Stable tests |
| beta | Beta versions of tests |

**Note:** You can use any versions of the TEAM Engine and executable test suites.
Just update the versions set in the variant configurations in `build.gradle`.
You can also add new variants. If you do not define a version for a test suite, it will not be included in that variant.

## Introduction

Running the Docker Containers with pre-installed OGC TEAM Engine and selected executable test suites from the provided Dockerfiles needs some preparations:

 * Install Docker
 * Build Docker Image with Gradle
 * Create and start Docker Container

The steps are described in the following sections.

### Requirements

The following software is required for the complete workflow (from git clone to the running Docker Container). The specified versions are the tested ones. Other versions should also work.

 * JDK 1.8
 * Git 2.9.3
 * Docker 1.12

## Prerequisites

Before you start to work with this project, Docker has to be installed and all dependencies be provided as described in the following sections.

### Install Docker

Check the official [Docker documentation](https://docs.docker.com/engine/) for information how to install Docker on your operating system. And then install Docker and supporting tools.

### Executable test suites

As some test suites or specific versions may not be be available via Maven Central, in this build the test modules are retrieved (and if necessary built) via the jitpack.io repository.
If a module is built for the first time, retrieving it will take longer because first the build takes place.
This approach relies on the source code being published on Github, in the *opengeospatial* organisation.

### Build TEAM Engine Docker Image

To build a Docker Image with TEAM Engine and all selected executable test suites run the Gradle task `buildImage`. By default the *production* variant is built.

```
./gradlew buildImage
```

There are also specific tasks generated for each variant, for example:

```
./gradlew build-production
```

```
./gradlew build-beta
```

## Running TEAM Engine inside a Docker Container

The following Docker command starts TEAM Engine with all available executable test suites inside a Docker Container on port 8081 with the previously built Docker Image for the *production* variant named `wetransform/teamengine:production`:

```
docker run -p 8081:8080 --rm wetransform/teamengine:production
```

## Accessing the TEAM Engine web interface

Use a browser of your choice and open the URL:

http://localhost:8081/teamengine


## Hints for usage in production

### Mount user data from host system

User data should be backed up regularly as they contain all registered users and the corresponding test runs.
An easy way to achieve this is to hold these data on the host system. By that they can be backed up by simple file system backups. Also, the data can easily be copied and used by different Docker Containers.

So, the user data folder must be mounted into the Docker Container.
With the `-v` flag a host system directory can be mounted as a data volume.

This is an example how to mount the `~/te_base/users` (Linux syntax) folder (is created if not existing) of host system into Docker Container:

```
docker run -p 8081:8080 -v ~/te_base/users:/root/te_base/users --rm wetransform/teamengine:production
```
