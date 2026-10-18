# Cloudflare DDNS Kubernetes Deployment Guide

This guide explains how to deploy and rollback the Cloudflare DDNS service on a Kubernetes cluster using the provided manifests and configuration files.

---

## 🚀 Deployment Steps

### 1. Prepare Your Configuration

- Edit or merge your Cloudflare configuration into a single file (e.g., `config-merged.json`).
- Example config files: `config-imsof.json`, `config-meliani.json`, `config-merged.json`.

### 2. Create a Kubernetes Secret

For the `imsof-cloudflare-dns` deployment:

```sh
# 1) Create the namespace
kubectl create namespace imsof-dns-services

# 2) Create the secret in that namespace (uses your merged config)
kubectl create secret generic config-cloudflare-ddns-imsof \
  --from-file=config.json=config-merged.json \
  -n imsof-dns-services
```

### 3. Deploy the Application

For the `imsof-cloudflare-dns` deployment:

```sh
# 3) Apply the deployment manifest
kubectl apply -f k8s/imsof-cloudflare-dns.yml
```

### 4. Verify the Deployment

```sh
# 4) Check rollout
kubectl -n imsof-dns-services get deploy,pods

# 5) Tail logs
kubectl -n imsof-dns-services logs -l app.kubernetes.io/name=imsof-cloudflare-dns --since=1m
```
- Ensure the pod is running and check logs for DNS update activity.

---

## 🔄 Rollback / Uninstall Steps

### 1. Remove the Deployment

For the `imsof-cloudflare-dns` deployment:

```sh
kubectl delete -f k8s/imsof-cloudflare-dns.yml
```
- This deletes the deployment and stops the DDNS updates.

### 2. Remove the Secret

```sh
kubectl delete secret config-cloudflare-ddns-imsof -n imsof-dns-services
kubectl delete namespace imsof-dns-services
```
- This removes the configuration secret and namespace from the cluster.

---

## 📝 Notes
- You can update the config by recreating the secret and reapplying the deployment.
- For multiple configs, merge them into a single file under the `cloudflare` array.
- The deployment uses the `timothyjmiller/cloudflare-ddns:latest` image by default.
- Make sure your Kubernetes context is set to the correct cluster before running commands.

---

## 📚 References
- `k8s/imsof-cloudflare-dns.yml` — Deployment manifest for `imsof-cloudflare-dns`
- `config-merged.json` — Example merged configuration
- [Cloudflare DDNS GitHub](https://github.com/timothyjmiller/cloudflare-ddns)

---

**For issues, check pod logs and configuration file formatting.**
