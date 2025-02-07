```
`services:
    linuxserver:
        privileged: true
        container_name: DILGERJF
        dns:
         - 192.168.50.250
        environment:
            - PUID=0
            - PGID=0
            - TZ=America/New_York
            - JELLYFIN_DATA_DIR=/config/data
            - JELLYFIN_CONFIG_DIR=/config
            - JELLYFIN_LOG_DIR=/config/log
            - JELLYFIN_CACHE_DIR=/config/cache
        healthcheck:
           test: curl -i http://localhost/health
        volumes:
            - '/var/lib/jellyfin:/config'
            - 'jellyfinmovies:/media/Movies'
            - 'jellyfintvshows:/media/TV\ Shows'
        restart: unless-stopped
        image: docker.io/jellyfin/jellyfin
        labels:
          traefik.enable: true
          traefik.http.routers.jellyfin.entryPoints: https
          traefik.http.routers.jellyfin.rule: Host(`jellyfin.cameronxdx.net`)
          traefik.http.routers.jellyfin.tls: true
          traefik.http.routers.jellyfin.tls.certResolver: le
          traefik.http.routers.jellyfin.middlewares: authentik@docker
          traefik.http.services.jellyfin-svc.loadbalancer.server.port: 80
          traefik.http.services.jellyfin-svc.loadbalancer.server.scheme: http

        networks:
         traefik_default: 
         external_network:
          ipv4_address: 192.168.50.209


    

networks:
 traefik_default:
  name: traefik_default
  external: true
 external_network:
  name: traefik_network
  external: true

volumes: 
  jellyfinmovies:
   driver_opts:
    type: "nfs"
    o: "addr=192.168.50.203,rw,noatime,rsize=8192,wsize=8192,tcp,timeo=14,nfsvers=4"
    device: ":/Movies"
    
  jellyfintvshows:
   driver_opts:
    type: "nfs"
    o: "addr=192.168.50.203,rw,noatime,rsize=8192,wsize=8192,tcp,timeo=14,nfsvers=4"
    device: ":/TV Shows"`
```