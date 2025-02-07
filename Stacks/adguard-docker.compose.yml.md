```
`services:
  adguard:
    image: adguard/adguardhome
    restart: always
    networks: 
     external_network:
        ipv4_address: 192.168.50.250
     internal-net:
    volumes:
    - AdguardDATA:/opt/adguardhome/work
    - AdguardCONF:/opt/adguardhome/conf
    labels:
     traefik.enable: true
     traefik.http.routers.adguard.entryPoints: https,http
     traefik.http.routers.adguard.rule: Host(`dns.cameronxdx.net`)
     traefik.http.routers.adguard.tls: true
     traefik.http.routers.adguard.tls.certResolver: le 
     traefik.http.services.adguard.loadbalancer.server.port: 443
     traefik.docker.network: traefik_default
     traefik.http.routers.adguard.middlewares: authentik@docker
volumes:
  AdguardDATA:

  AdguardCONF:
networks:
  external_network:
    name: traefik_network
    external: true
  internal-net:
   name: traefik_default
   external: true`
```