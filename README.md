# Guestbook CI/CD Pipeline

**Project:** Cloud-Native Guestbook with Automated Deployment  
**Course:** Containerteknologi DevOps 24  
**Team:** Anwar Mousa , Vineeta and Yaser Salha  
**Date:** November 30, 2025

## Overview

This project implements a complete CI/CD pipeline for the Guestbook application using GitHub Actions, deploying automatically to Red Hat OpenShift Sandbox.

## Architecture
```
GitHub Push → GitHub Actions → Build Containers → Push to GHCR → Deploy to OpenShift
```

### Components

- **Backend:** Go REST API (2 replicas)
- **Frontend:** Nginx web server (2 replicas)
- **PostgreSQL:** Database with persistent storage
- **Redis:** Cache layer

## CI/CD Pipeline

### Trigger
Pipeline runs automatically on:
- Push to `main` branch
- Pull requests to `main` branch

### Pipeline Steps

1. **Build Backend Container**
   - Multi-stage Docker build
   - Go 1.24 Alpine base
   - Final image: ~15MB

2. **Build Frontend Container**
   - Nginx Alpine base
   - Static assets + configuration

3. **Push to GitHub Container Registry**
   - Backend: `ghcr.io/ysalha2003/ocp-guestbook-cicd/backend:latest`
   - Frontend: `ghcr.io/ysalha2003/ocp-guestbook-cicd/frontend:latest`

4. **Deploy to OpenShift**
   - Updates deployment images
   - Triggers rolling update
   - Verifies deployment success

## Application URL

**Production:** https://frontend-ysalha-dev.apps.rm3.7wse.p1.openshiftapps.com

## Local Development

### Prerequisites
- Git
- Docker or Podman
- OpenShift CLI (`oc`)

### Clone Repository
```bash
git clone https://github.com/ysalha2003/ocp-guestbook-cicd.git
cd ocp-guestbook-cicd
```

### Build Locally
```bash
# Backend
docker build -t guestbook-backend:dev ./backend

# Frontend
docker build -t guestbook-frontend:dev ./frontend
```

### Deploy to OpenShift
```bash
# Login to OpenShift
oc login --token=YOUR_TOKEN --server=YOUR_SERVER

# Apply manifests
oc apply -f manifests/
```

## Deployment Structure
```
manifests/
├── postgresql.yaml    # Database + PVC + Service
├── redis.yaml         # Cache + Service
├── backend.yaml       # Backend Deployment + Service
├── frontend.yaml      # Frontend Deployment + Service
└── route.yaml         # HTTPS Route
```

## Secrets Management

Required secrets in GitHub:
- `OPENSHIFT_SERVER`: OpenShift API endpoint
- `OPENSHIFT_TOKEN`: Service account token
- `OPENSHIFT_NAMESPACE`: Project namespace

Required secrets in OpenShift:
- `postgresql-secret`: Database credentials
- `redis-secret`: Cache password

## Monitoring

### Check deployment status
```bash
oc get pods
oc get deployments
oc rollout status deployment/backend
oc rollout status deployment/frontend
```

### View logs
```bash
oc logs deployment/backend
oc logs deployment/frontend
```

### Access application
```bash
oc get route frontend
```

## Bonus Features

### Smart Deployment
- Only modified components are redeployed
- ConfigMap changes trigger pod restart automatically
- Database migrations preserve data integrity

### Change Detection
```bash
# If only backend changed:
git diff --name-only HEAD~1 | grep backend/
# Result: Only backend rebuilds and deploys

# If ConfigMap changed:
oc apply -f manifests/configmap.yaml
oc rollout restart deployment/backend  # Restart to pick up new config
```

## Troubleshooting

### Pipeline fails on push
- Check GitHub Actions logs
- Verify OpenShift token is valid
- Ensure namespace exists

### Pods not starting
```bash
oc describe pod POD_NAME
oc logs POD_NAME
```

### Image pull errors
- Verify GHCR packages are public
- Check image names match manifest

## Team Contributions

**Yaser Salha:**
- CI/CD pipeline implementation
- OpenShift deployment configuration
- Documentation
- Testing and verification
- 
**Anwar Mousa:**
- Backend application development
- Database and Redis integration
- Multi-stage Docker build optimization
- GHCR package configuration

**Vineeta:**
- Frontend application development  
- Nginx configuration and deployment
- Kubernetes manifest creation
- Resource limits and health checks

## License

Educational project for Containerteknologi DevOps 24 course.

## References

- Repository: https://github.com/jonasbjork/ocp-guestbook
- OpenShift Documentation: https://docs.openshift.com
- GitHub Actions: https://docs.github.com/actions
