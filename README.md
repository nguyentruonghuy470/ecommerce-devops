# E-Commerce DevOps Platform

Production-style DevOps project for deploying, operating, monitoring, scaling, and backing up a full-stack e-commerce application on Kubernetes.

## Overview

This project demonstrates a complete Kubernetes-based DevOps environment for a full-stack e-commerce application.

The platform includes:

- Angular frontend
- Spring Boot backend
- MariaDB database
- Redis Sentinel
- Kubernetes
- NGINX Load Balancer
- NGINX Ingress Controller
- NFS persistent storage
- Horizontal Pod Autoscaler
- Prometheus
- Grafana
- Uptime Kuma
- Velero
- MinIO
- GitHub Actions CI

---

## Architecture

```text
                           Internet
                              |
                              v
                     NGINX Load Balancer
                              |
                              v
                   NGINX Ingress Controller
                         /          \
                        /            \
                       v              v
              Frontend Service    Backend Service
                       |              |
                       v              v
                 Angular + Nginx  Spring Boot
                                      |
                              +-------+-------+
                              |               |
                              v               v
                           MariaDB          Redis
                              |           Sentinel
                              |               |
                              v               v
                         NFS Storage      NFS Storage


                    Kubernetes Platform
                           |
          +----------------+----------------+
          |                |                |
          v                v                v
        HPA           Prometheus         Grafana
                                           |
                                           v
                                      Monitoring


                    Backup / Disaster Recovery
                           |
                           v
                         Velero
                           |
                           v
                         MinIO
