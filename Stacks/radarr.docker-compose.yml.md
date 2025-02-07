```
services:
  sonarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: Radarr
    environment:
      - PUID=0
      - PGID=0
      - TZ=America/New_York
    labels:
      traefik.enable: true
      traefik.http.routers.sonarr.entryPoints: https
      traefik.http.routers.sonarr.rule: Host(`radarr.cameronxdx.net`)
      traefik.http.routers.sonarr.tls: true
      traefik.http.routers.sonarr.tls.certResolver: le
      traefik.http.routers.sonarr.middlewares: authentik@docker
      traefik.http.services.sonarr-svc.loadbalancer.server.port: 7878
    volumes:
      - "RadarrDownloads:/downloads"
      - "RadarrMovies:/movies"
      - "/var/opt/radarr:/config"
    networks:
     traefik_internal:
     
    restart: unless-stopped
    
networks:
 traefik_internal:
  name: traefik_default
  external: true
  
volumes:
 RadarrMovies:
  driver_opts:
    type: "nfs"
    o: "addr=192.168.50.203,rw,noatime,rsize=8192,wsize=8192,tcp,timeo=14,nfsvers=4" 
    device: ":/TV\ Shows"
 RadarrDownloads:
  driver_opts:
    type: "nfs"
    o: "addr=192.168.50.203,rw,noatime,rsize=8192,wsize=8192,tcp,timeo=14,nfsvers=4" 
    device: ":/Downloads"
  

```