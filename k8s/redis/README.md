# Redis Sentinel

Redis High Availability deployment for the ecommerce platform.

## Architecture

```text
                    ┌─────────────────────┐
                    │   Redis Service     │
                    │     ClusterIP       │
                    │ 6379 / 26379        │
                    └──────────┬──────────┘
                               │
                ┌──────────────┴──────────────┐
                │                             │
        ┌───────▼────────┐           ┌────────▼───────┐
        │ Redis Node 0   │           │ Redis Node 1   │
        │ Master         │◄──────────│ Replica        │
        │ :6379          │Replication│ :6379         │
        │ Sentinel       │           │ Sentinel       │
        │ :26379         │           │ :26379         │
        └───────┬────────┘           └────────┬───────┘
                │                             │
                └──────────────┬──────────────┘
                               │
                       ┌───────▼───────┐
                       │  NFS Storage  │
                       │    10Gi RWX   │
                       └───────────────┘
