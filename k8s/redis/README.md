# Redis Sentinel

Redis High Availability deployment for the ecommerce platform using Redis 8.10.1, Redis Sentinel, Kubernetes StatefulSet, and NFS persistent storage.

## Architecture

```text
                         ┌─────────────────────────────┐
                         │       Redis Service         │
                         │         ClusterIP           │
                         │       6379 / 26379          │
                         └──────────────┬──────────────┘
                                        │
                         ┌──────────────┴──────────────┐
                         │                             │
                 ┌───────▼────────┐           ┌────────▼───────┐
                 │ Redis Node 0   │           │ Redis Node 1   │
                 │    Replica     │           │     Master     │
                 │     :6379      │◄──────────│     :6379      │
                 │   Sentinel     │ Replication│   Sentinel     │
                 │    :26379      │           │    :26379      │
                 └───────┬────────┘           └────────┬───────┘
                         │                             │
                         └──────────────┬──────────────┘
                                        │
                              ┌─────────▼─────────┐
                              │    NFS Storage    │
                              │      10Gi RWX     │
                              └───────────────────┘
