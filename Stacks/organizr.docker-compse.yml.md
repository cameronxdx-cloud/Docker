```
version: "2"
services:
  organizr:
    image: organizr/organizr:latest
    container_name: organizr
    restart: unless-stopped
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
      - UMASK_SET=022 #optional
    #user: "999"
    volumes:
      - organizrconfig:/config
    networks:
      traefik_default:
    labels:
      traefik.enable: true
      traefik.http.routers.organizr.entrypoints: https,http
      traefik.http.routers.organizr.rule: Host(`organizr.cameronxdx.net`)
      traefik.http.routers.organizr.middlewares: authentik@docker
      traefik.http.routers.organizr.tls: true
      traefik.http.routers.organizr.tls.certresolver: le
      traefik.http.services.organizr-svc.loadBalancer.server.port: 80
      traefik.http.services.organizr-svc.loadBalancer.server.scheme: http
      
volumes:
 organizrconfig:
networks:
 traefik_default:
  name: traefik_default
  external: true
 Traefik:
  name: traefik_network
  external: true


```