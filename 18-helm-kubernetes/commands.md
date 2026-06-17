# Commands — Lab 18: Helm in Kubernetes

---

## 🟦 Step 1 — Verify Kubernetes Cluster

```bash
kubectl get nodes
kubectl get pods -A
```

---

## 🟦 Step 2 — Install Helm

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Verify installation
helm version
```

---

## 🟦 Step 3 — Deploy NGINX Using Helm (OCI-Based Chart)

```bash
helm install my-nginx oci://registry-1.docker.io/bitnamicharts/nginx \
  -n helm-demo \
  --create-namespace
```

> **Note:** You may see Bitnami warnings related to production recommendations or rolling image tags. These can be safely ignored for this demo.

### Verify the release and resources
```bash
helm list -n helm-demo
kubectl get all -n helm-demo
```

---

## 🟦 Step 4 — Check Pods and Service

```bash
kubectl get pods -n helm-demo
kubectl get svc -n helm-demo
```

> **Note:** The service type is `LoadBalancer` by default. In a local kubeadm cluster, `EXTERNAL-IP` will remain in `pending` state — this is expected. We will change it to `NodePort` in the next steps.

---

## 🟦 Step 5 — View Default Chart Values

```bash
# Full values (output is long)
helm show values oci://registry-1.docker.io/bitnamicharts/nginx

# Filtered view — replica count section only
helm show values oci://registry-1.docker.io/bitnamicharts/nginx | grep replicaCount -A 3
```

---

## 🟦 Step 6 — Create Custom values.yaml

```bash
vi custom-values.yaml
```

File content:
```yaml
replicaCount: 2

service:
  type: NodePort
  nodePorts:
    http: 30080
```

> This file is already included in this lab folder — use `custom-values.yaml` directly.

---

## 🟦 Step 7 — Upgrade Release Using Custom Values

```bash
helm upgrade my-nginx oci://registry-1.docker.io/bitnamicharts/nginx \
  -n helm-demo \
  -f custom-values.yaml
```

### Verify after upgrade
```bash
kubectl get pods -n helm-demo
kubectl get svc -n helm-demo
```

**Expected result:**
- 2 NGINX pod replicas running
- Service type changed to `NodePort`
- NodePort `30080` assigned

---

## 🟦 Step 8 — Access the Application

```bash
# Get NodePort
kubectl get svc -n helm-demo

# Get Worker Node IP
kubectl get nodes -o wide
```

Open in browser:
```
http://<worker-node-ip>:30080
```

> Example: `http://192.168.56.116:30080`

**Expected:** NGINX Welcome Page loads successfully.

---

## 🟦 Step 9 — View Helm Release History

```bash
helm history my-nginx -n helm-demo
```

**Expected output:**
- Revision 1 — initial installation — `superseded`
- Revision 2 — after upgrade — `deployed`

---

## 🟦 Step 10 — Perform Rollback

### Check current state before rollback
```bash
kubectl get pods -n helm-demo
kubectl get svc -n helm-demo
```

### Rollback to revision 1
```bash
helm rollback my-nginx 1 -n helm-demo
```

### Verify after rollback
```bash
kubectl get pods -n helm-demo
kubectl get svc -n helm-demo
helm history my-nginx -n helm-demo
```

**Expected after rollback:**
- Replica count returns to 1
- Service type changes back to `LoadBalancer`
- Helm adds a new revision entry for the rollback itself

---

## 🟦 Step 11 — Uninstall Helm Release

```bash
helm uninstall my-nginx -n helm-demo

# Verify release is removed
helm list -n helm-demo
kubectl get all -n helm-demo
```

---

## 🟦 Step 12 — Final Cleanup

```bash
kubectl delete namespace helm-demo

# Verify
kubectl get ns
```
