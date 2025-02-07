```
services:
  reverse-proxy:
    # The official v2 Traefik docker image
    image: traefik:latest
    hostname: traefik.cameronxdx.net
    dns:
     - 192.168.50.250
    volumes:
      # So that Traefik can listen to the Docker events
      - /var/run/docker.sock:/var/run/docker.sock
      - /var/opt/traefik/:/etc/traefik/
      - traefik-ssl-certs:/ssl-certs
    labels:
      traefik.enable: true
      traefik.providers: docker
      traefik.api.dashboard: true
      traefik.api: true
      traefik.http.routers.api.rule: Host(`traefik.cameronxdx.net`) && (PathPrefix(`/api`) || PathPrefix(`/dashboard`))
      traefik.http.routers.api.service: api@internal
      traefik.http.routers.api.entrypoints: http,https
      traefik.http.routers.api.tls: true
      traefik.http.routers.api.tls.certresolver: le

    networks: 
     internal_network:
     external_network:
        ipv4_address: 192.168.50.201
    restart: unless-stopped
      
volumes:
 traefik-ssl-certs:
  driver: local

networks:

  internal_network:
    name: traefik_default

  external_network:
    name: traefik_network
    external: true
```