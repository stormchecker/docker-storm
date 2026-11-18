# Dockerfiles related to Storm

This repository provides Dockerfiles for the base images used by Storm.
The images are hosted at the [GitHub Container Registry](https://github.com/orgs/stormchecker/packages) (`ghcr.io/stormchecker`) and are also mirrored at the [movesrwth organization](https://hub.docker.com/u/movesrwth) on DockerHub.
For the libraries [storm](https://github.com/stormchecker/storm), [stormpy](https://github.com/stormchecker/stormpy) and [carl-storm](https://github.com/stormchecker/carl-storm), Dockerfiles and dedicated CI workflows for creating and deploying Docker images are available in the respective repositories.

## Image creation
Docker images are automatically created by the CI for each push to `main` and on a monthly schedule. The CI workflows push every image to both registries, using `GITHUB_TOKEN` for GHCR and the `STORM_CI_DOCKER_*` secrets for DockerHub. The images are built via `docker/github-builder`, which also signs the provenance.
Each `storm-basesystem` image is tagged by its underlying OS (e.g. `ubuntu-26.04`), plus `latest` and `minimal_dependencies`.
Each `storm-dependencies` image is tagged `latest` and also with the build date (`YYYYMMDD`).

The build of Docker images can also be manually triggered via [GitHub Actions](https://github.com/stormchecker/docker-storm/actions/).
- `Build basesystem`: builds all base systems for Storm.
- `Build base with dependencies`: builds Docker image containing all dependencies for Storm.

## File structure
- [storm-basesystem](storm-basesystem): Dockerfiles for the Linux base images with the dependencies required by Storm.
- [storm-dependencies](storm-dependencies): Dockerfile preparing the dependencies for Storm (Spot, MathSAT, SoPlex, etc.)

## Configuration options
- The configuration options are given at the top of each Dockerfile.
   A configuration `arg_name` can be changed from the commandline by adding `--build-arg <arg_name>=<value>`.
- Common configuration options are:
    * The base image `BASE_IMAGE` used for the Dockerfile.
    * Library versions `X_VERSION`

## Update images
The following steps should be performed to update the Docker images.

### Update storm-basesystem
* Add new versions of Ubuntu and Debian as base images to the CI configuration file `.github/workflows/basesystem.yml`.
* Remove old versions of Ubuntu and Debian which are not supported anymore from the same configuration file.
* The CI automatically creates new images on pushes to `main`.

### Update storm-dependencies
* Update the versions of the dependencies used in `storm-dependencies/Dockerfile`:
  * [cvc5](https://github.com/cvc5/cvc5)
  * [Gurobi](https://github.com/Gurobi/docker-optimizer/tree/master)
  * [HiGHS](https://github.com/ERGO-Code/HiGHS)
  * [MathSAT](https://mathsat.fbk.eu/download.html)
  * [Soplex](https://soplex.zib.de/)
  * [Spot](https://spot.lre.epita.fr/install.html)
* The CI automatically creates new images on pushes to `main`.
