# Dockerisation de Mkdocs

## docker-compose.yaml

```yaml
version: '3'

services:
  mkdocs:
    container_name: mkdocs
    image: polinux/mkdocs:1.4.2
    restart: always
    ports:
      - 8000
    environment:
      LIVE_RELOAD_SUPPORT: 'true'
      ADD_MODULES: 'fontawesome-markdown mkdocs-git-revision-date-localized-plugin mkdocs-material'
      FAST_MODE: 'true'
      DOCS_DIRECTORY: '/mkdocs'
      GIT_REPO: 'git@github.com:sixadmin/mkdocs.git'
      GIT_BRANCH : 'main'
      UPDATE_INTERVAL: 5
      AUTO_UPDATE: 'true'
    volumes:
      - $HOME/.ssh/id_rsa:/root/.ssh/id_rsa
    networks:
      - read
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=read"
      - "traefik.http.routers.mkdocs.entrypoints=web,websecure"
      - "traefik.http.routers.mkdocs.rule=Host(`readthedocs.cruz.im`)"
      - "traefik.http.services.mkdocs.loadbalancer.server.port=8000"
      - "traefik.http.routers.mkdocs.tls=true"
      - "traefik.http.routers.mkdocs.tls.certresolver=richard"

networks:
  read:
    external: true
```