# Commands — Lab 21: Access Grafana Externally | NodePort vs LoadBalancer vs Ingress

---

## 🟦 Step 1 — Verify Existing Monitoring Setup

```bash
kubectl get pods -n monitoring
kubectl get svc -n monitoring
```

> Grafana service is currently `ClusterIP` — only accessible from inside the cluster.

---

## 🟦 Recap — Port Forwarding (Previous Method)

```bash
# Get Grafana admin password
kubectl get secret monitoring-grafana -n monitoring \
  -o jsonpath="{.data.admin-password}" | base64 -d; echo ""

# Port-forward Grafana (terminal must stay active)
kubectl port-forward --address 0.0.0.0 \
  svc/monitoring-grafana \
  3000:80 -n monitoring
```

**Browser URL:** `http://<MASTER_NODE_IP>:3000`

> Port-forward creates a temporary connection. Closing the terminal breaks access.

---

## 🟦 Method 1 — NodePort

### Verify current service
```bash
kubectl get svc monitoring-grafana -n monitoring
```

### Change service type to NodePort
```bash
kubectl patch svc monitoring-grafana \
  -n monitoring \
  -p '{"spec":{"type":"NodePort"}}'
```

### Get assigned NodePort and node IP
```bash
kubectl get svc monitoring-grafana -n monitoring
kubectl get nodes -o wide
```

**Browser URL:** `http://<NODE-IP>:<NODEPORT>`

Example: `http://192.168.56.115:32010`

> NodePort is simple but requires remembering the port number. Not preferred for production.

---

## 🟦 Method 2 — LoadBalancer (MetalLB)

### Verify MetalLB is running
```bash
kubectl get pods -n metallb-system
kubectl get ipaddresspools -n metallb-system
kubectl get ipaddresspool -n metallb-system -o yaml
```

### Change service type to LoadBalancer
```bash
kubectl patch svc monitoring-grafana \
  -n monitoring \
  -p '{"spec":{"type":"LoadBalancer"}}'
```

### Verify external IP assigned by MetalLB
```bash
kubectl get svc monitoring-grafana -n monitoring
```

> MetalLB assigns an IP from the configured pool (e.g. `192.168.56.240`).

**Browser URL:** `http://192.168.56.240`

> No port number needed. But users still need to remember the IP address.

---

## 🟦 Method 3 — Ingress (NGINX Ingress Controller)

### Step 3.1 — Install NGINX Ingress Controller

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  -n ingress-nginx \
  --create-namespace
```

### Verify installation
```bash
kubectl get pods -n ingress-nginx
kubectl get svc -n ingress-nginx
```

> The `ingress-nginx-controller` service gets a LoadBalancer IP from MetalLB (e.g. `192.168.56.241`). This is the IP all Ingress traffic flows through.

---

### Step 3.2 — Create Grafana Ingress Resource

```bash
cat grafana-ingress.yaml
kubectl apply -f grafana-ingress.yaml
```

### Verify Ingress
```bash
kubectl get ingress -n monitoring
kubectl describe ingress grafana-ingress -n monitoring
```

### Verify Grafana pod
```bash
kubectl get pods -n monitoring -o wide | grep grafana
```

---

### Step 3.3 — Configure Local DNS (Hosts File)

Add the following entry to your hosts file:

```
192.168.56.241   grafana.techopstutorials.local
```

**Linux:**
```bash
sudo vi /etc/hosts
```

**Windows** (Notepad as Administrator):
```
C:\Windows\System32\drivers\etc\hosts
```

**Verify DNS resolution (Windows PowerShell):**
```powershell
Resolve-DnsName grafana.techopstutorials.local
```

---

### Step 3.4 — Retrieve Grafana Password

```bash
kubectl get secret monitoring-grafana \
  -n monitoring \
  -o jsonpath="{.data.admin-password}" | base64 -d; echo ""
```

---

### Step 3.5 — Access Grafana via Hostname

**Browser URL:** `http://grafana.techopstutorials.local`

Login:
- Username: `admin`
- Password: retrieved from the command above

---

## 🟦 Verification

### Verify Ingress address
```bash
kubectl get ingress -n monitoring
```

### Verify Grafana backend endpoint
```bash
kubectl get endpoints monitoring-grafana -n monitoring
```

### Verify traffic through Ingress Controller logs
```bash
kubectl logs -n ingress-nginx deployment/ingress-nginx-controller
```

> Look for `grafana.techopstutorials.local` in the logs and the backend endpoint `10.244.1.2:3000`.

---

## 🧹 Cleanup

```bash
# Remove Ingress resource
kubectl delete -f grafana-ingress.yaml

# Remove NGINX Ingress Controller
helm uninstall ingress-nginx -n ingress-nginx
kubectl delete namespace ingress-nginx

# Revert Grafana service to ClusterIP
kubectl patch svc monitoring-grafana \
  -n monitoring \
  -p '{"spec":{"type":"ClusterIP"}}'
```
