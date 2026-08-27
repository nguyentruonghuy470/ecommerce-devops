# Uptime Kuma Monitoring

Uptime Kuma is deployed in the `monitoring` namespace to monitor the ecommerce platform and infrastructure services.

## Architecture

                    Internet
                       |
                       v
                NGINX Load Balancer
                       |
                       v
                 NGINX Ingress
                       |
                       v
              uptime.huy.tech.vn
                       |
                       v
              Uptime Kuma :3001
                       |
              +--------+--------+
              |                 |
              v                 v
        MariaDB Database    NFS Persistent
             kuma              Storage
