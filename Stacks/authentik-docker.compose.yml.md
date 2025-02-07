`services:
  postgresql:
    image: docker.io/library/postgres:12-alpine
    restart: unless-stopped
    dns:
     - 192.168.50.250
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -d $${POSTGRES_DB} -U $${POSTGRES_USER}"]
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 5s
    networks:
     internal:
    volumes:
      - database:/var/lib/postgresql/data
    environment:
      POSTGRES_PASSWORD: ${PG_PASS:?database password required}
      POSTGRES_USER: ${PG_USER:-authentik}
      POSTGRES_DB: ${PG_DB:-authentik}
    env_file:
      - stack.env
  redis:
    image: docker.io/library/redis:alpine
    dns:
     - 192.168.50.250
    command: --save 60 1 --loglevel warning
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "redis-cli ping | grep PONG"]
      start_period: 20s
      interval: 30s
      retries: 5
      timeout: 3s
    volumes:
      - redis:/data
    networks:
     internal:
  server:
    image: ${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2024.12.3}
    dns:
     - 192.168.50.250
    restart: unless-stopped
    command: server
    environment:
      AUTHENTIK_REDIS__HOST: redis
      AUTHENTIK_POSTGRESQL__HOST: postgresql
      AUTHENTIK_POSTGRESQL__USER: ${PG_USER:-authentik}
      AUTHENTIK_POSTGRESQL__NAME: ${PG_DB:-authentik}
      AUTHENTIK_POSTGRESQL__PASSWORD: ${PG_PASS}
    volumes:
      - media:/media
      - custom-templates:/templates
    env_file:
      - stack.env
    networks:
     internal:
     traefik_default:
    labels:
        traefik.enable: true
        traefik.docker.network: traefik_default
        # traefikv2
        traefik.http.routers.authentik.rule: Host(`auth.cameronxdx.net`) || HostRegexp(`{subdomain:[a-z0-9]+}.cameronxdx.net`) && PathPrefix(`/outpost.goauthentik.io/`)
        traefik.http.routers.authentik.entrypoints: https
        traefik.http.routers.authentik.tls: true
        traefik.http.routers.authentik.tls.certresolver: le
        traefik.http.services.authentik.loadbalancer.server.port: 9000
        traefik.http.middlewares.authentik.forwardauth.address: http://authentik-server-1:9000/outpost.goauthentik.io/auth/traefik
        traefik.http.middlewares.authentik.forwardauth.trustforwardheader: true
        traefik.http.middlewares.authentik.forwardauth.authresponseheaders: X-authentik-username,X-authentik-groups,X-authentik-email,X-authentik-name,X-authentik-uid,X-authentik-jwt,X-authentik-meta-jwks,X-authentik-meta-outpost,X-authentik-meta-provider,X-authentik-meta-app,X-authentik-meta-version
    depends_on:
      - postgresql
      - redis
  worker:
    image: ${AUTHENTIK_IMAGE:-ghcr.io/goauthentik/server}:${AUTHENTIK_TAG:-2024.12.3}
    restart: unless-stopped
    dns:
     - 192.168.50.250
    command: worker
    networks:
     internal:
    environment:
      AUTHENTIK_REDIS__HOST: redis
      AUTHENTIK_POSTGRESQL__HOST: postgresql
      AUTHENTIK_POSTGRESQL__USER: ${PG_USER:-authentik}
      AUTHENTIK_POSTGRESQL__NAME: ${PG_DB:-authentik}
      AUTHENTIK_POSTGRESQL__PASSWORD: ${PG_PASS}
    # `user: root` and the docker socket volume are optional.
    # See more for the docker socket integration here:
    # https://goauthentik.io/docs/outposts/integrations/docker
    # Removing `user: root` also prevents the worker from fixing the permissions
    # on the mounted folders, so when removing this make sure the folders have the correct UID/GID
    # (1000:1000 by default)
    user: root
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - media:/media
      - certs:/certs
      - custom-templates:/templates
    env_file:
      - stack.env
    depends_on:
      - postgresql
      - redis

volumes:
  database:
    driver: local
  redis:
    driver: local
  media:
  certs:
  custom-templates:


networks:
 traefik_default:
  name: traefik_default
  external: true
 internal:`