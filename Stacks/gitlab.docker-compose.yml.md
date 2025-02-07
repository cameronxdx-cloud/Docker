```
services:
  gitlab:
    image: gitlab/gitlab-ce:17.5.1-ce.0
    container_name: gitlab-demo
    hostname: 'gitlab-demo'
    volumes:
      - gitlab-config:/etc/gitlab
      - gitlab-logs:/var/log/gitlab
      - gitlab-data:/var/opt/gitlab
    shm_size: '256m'
    restart: unless-stopped
    networks:
     traefik_default:
     traefik_network:
      ipv4_address: 192.168.50.202 #Make sure you port forward 22 for SSH access.
    labels:
     traefik.enable: true
     traefik.http.routers.gitlab.entryPoints: https
     traefik.http.routers.gitlab.rule: Host(`gitlab.cameronxdx.net`)
     traefik.http.routers.gitlab.tls: true
     traefik.http.routers.gitlab.tls.certResolver: le
     traefik.http.services.gitlab-svc.loadbalancer.server.port: 80
     traefik.docker.network: traefik_default
    environment:
      GITLAB_OMNIBUS_CONFIG: |
       external_url 'https://gitlab.cameronxdx.net'
       letsencrypt['enable'] = false
       nginx['listen_port'] = 80
       nginx['listen_https'] = false
       gitlab_rails['smtp_enable'] = true
       gitlab_rails['smtp_address'] = 'cameronxdx-net.mail.protection.outlook.com'
       gitlab_rails['smtp_port'] = 25
       gitlab_rails['smtp_domain'] = 'cameronxdx-net.mail.protection.outlook.com'
       gitlab_rails['smtp_tls'] = false
       gitlab_rails['smtp_openssl_verify_mode'] = 'none'
       gitlab_rails['smtp_enable_starttls_auto'] = false
       gitlab_rails['smtp_ssl'] = false
       gitlab_rails['smtp_force_ssl'] = false
       gitlab_rails['smtp_user_name'] = "gitlab@cameronxdx.net"


networks:
 traefik_default:
  external: true
 traefik_network:
  external: true


volumes:
  gitlab-config:
  gitlab-logs:
  gitlab-data:
```