`services:
  chromium:
    image: lscr.io/linuxserver/chromium:latest
    container_name: chromium
    security_opt:
      - seccomp:unconfined #optional
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=America/New_York
      - NO_DECOR=true
      #- CHROME_CLI=
    volumes:
     - chromeConfig:/config
 #   ports:
 #    - 3000:3000
 #    - 3001:3001
    networks:
     traefik_default:
     Traefik:
      ipv4_address: 192.168.50.2
    shm_size: "1gb"
    restart: unless-stopped
    labels:
      traefik.enable: true
      traefik.http.routers.chrome.entrypoints: https,http
      traefik.http.routers.chrome.rule: Host(`browse.cameronxdx.net`)
      traefik.http.routers.chrome.middlewares: authentik@docker
      traefik.http.routers.chrome.tls: true
      traefik.http.routers.chrome.tls.certresolver: le
      traefik.http.services.chrome-svc.loadBalancer.server.port: 3001
      traefik.http.services.chrome-svc.loadBalancer.server.scheme: https

volumes:
 chromeConfig:
networks:
 traefik_default:
  name: traefik_default
  external: true
 Traefik:
  name: traefik_network
  external: true`