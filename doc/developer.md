# Developer information

- Images are published to GHCR (`ghcr.io/stormchecker/...`) and mirrored to [DockerHub](https://hub.docker.com/). The CI workflows push to both, using `GITHUB_TOKEN` for GHCR and the `STORM_CI_DOCKER_*` secrets for DockerHub. The images are created via `docker/github-builder` which also signs the provenance.
- To upload manually you need write access to the GHCR packages (login with a personal access token with `write:packages`) and, for DockerHub, an account that is a member of the `stormdev` team.
