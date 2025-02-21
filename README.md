# EdStock UI Deployment Guide

## Overview

This repository contains the frontend of the EdStock application. This repository contains all necessary configurations and scripts to deploy to an AWS EKS cluster using Jenkins and Kubernetes.

## Directory Structure

```bash
└── 📁edstock-ui
    └── 📁app
    └── 📁k8s
        └── frontend-deployment.yaml
        └── frontend-service.yaml
    └── Dockerfile
    └── Jenkinsfile
    └── package.json
    └── tailwind.config.ts
    └── tsconfig.json
```

## Deployment Process

The deployment is very similar to the deployment backend API; see [edstock-api](https://github.com/korngsamnang/edstock-api).
