# E-Commerce DevOps Project

A full-stack e-commerce application deployed and operated on Kubernetes with containerization, ingress routing, persistent storage, autoscaling, monitoring, and backup/restore capabilities.

## Architecture

```text
Internet
    |
    v
NGINX Load Balancer
    |
    v
NGINX Ingress Controller
    |
    +-------------------+
    |                   |
    v                   v
Frontend Service    Backend Service
    |                   |
    v                   v
Angular + Nginx     Spring Boot
                        |
                        v
                    MariaDB
                        |
                        v
                    NFS Storage

Kubernetes Platform
|
+-- HPA
+-- Prometheus
+-- Grafana
+-- Uptime Kuma
+-- Velero
+-- MinIO
