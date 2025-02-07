```
services:
  qbittorrent:
    image: trigus42/qbittorrentvpn
    container_name: qBitTorrent
    hostname: qbittorrent.cameronxdx.net
    environment:
      - VPN_ENABLED=yes
      - VPN_TYPE=openvpn
      - VPN_USERNAME=${OPENVPN_USERNAME}
      - VPN_PASSWORD=${OPENVPN_PASSWORD}
      - LAN_NETWORK=192.168.50.0/24
      - PUID=0
      - PGID=0
    networks:
     torrentINT:
    labels:
     - traefik.enable=true
     - traefik.http.routers.qBitTorrent.rule=host(`torrent.cameronxdx.net`)
     - traefik.http.routers.qBitTorrent.entryPoints=https
     - traefik.http.routers.qBitTorrent.tls=true
     - traefik.http.routers.qBitTorrent.tls.certResolver=le
     - traefik.http.routers.qBitTorrent.middlewares=authentik@docker
    volumes:
      - "TorrentTMP:/torrenttemp"
      - "TorrentDownloads:/downloads"
      - "/var/opt/qbittorrent:/config"
      - "/etc/localtime:/etc/localtime:ro"
    restart: unless-stopped
    cap_add:
      - NET_ADMIN
      
networks:
 torrentINT:
  name: traefik_default
  external: true
volumes:
  TorrentTMP:
  TorrentDownloads:
   driver_opts:
    type: "nfs"
    o: "addr=192.168.50.203,rw,noatime,rsize=8192,wsize=8192,tcp,timeo=14,nfsvers=4" 
    device: ":/Downloads"
```