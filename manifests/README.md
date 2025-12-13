# Kubernetes Manifests

This directory contains all Kubernetes/OpenShift manifest files for deploying the Guestbook application.

## Files Overview

### Application Components

**backend.yaml**
- Backend API deployment (2 replicas)
- Service configuration
- Health checks (liveness/readiness probes)
- Resource limits and requests
- Image pull secrets for private GHCR

**frontend.yaml**
- Frontend web server deployment (2 replicas)
- Service configuration
- Health checks (liveness/readiness probes)
- Resource limits and requests
- Image pull secrets for private GHCR

### Data Layer

**postgresql.yaml**
- PostgreSQL database deployment
- Persistent volume claim for data storage
- Service configuration
- Environment variables and secrets

**redis.yaml**
- Redis cache deployment
- Service configuration
- Environment variables

### Configuration

**configmap.yaml**
- Backend configuration settings
- Environment-specific values
- Application parameters

**route.yaml**
- OpenShift route for external access
- Frontend ingress configuration
- TLS/SSL termination settings

## Deployment Order

1. ConfigMap (configuration data)
2. PostgreSQL (database)
3. Redis (cache)
4. Backend (API)
5. Frontend (web server)
6. Route (external access)

## Usage

Deploy all manifests:
```bash
oc apply -f manifests/
```

Deploy specific component:
```bash
oc apply -f manifests/backend.yaml
```

## Important Notes

- All deployments include health checks
- Resource limits are set for production stability
- Private GHCR images require imagePullSecrets
- Persistent storage is used for PostgreSQL data
