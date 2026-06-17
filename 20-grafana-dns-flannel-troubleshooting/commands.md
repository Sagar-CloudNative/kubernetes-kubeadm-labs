# Commands — Lab 20: Grafana Plugin Error | DNS, CoreDNS & Flannel Troubleshooting

---

## 🟦 Step 1 — Show the Problem

Open Grafana in browser and show the plugin error on dashboards.

---

## 🟦 Step 2 — Verify Monitoring Pods

```bash
kubectl get pods -n monitoring
```

> All pods appear Running — the issue is not visible from pod status alone.

---

## 🟦 Step 3 — Check Grafana Logs

```bash
# Get Grafana pod name
kubectl get pods -n monitoring

# Check logs for timeout errors
kubectl logs -n monitoring <grafana-pod-name> | grep timeout
```

**Look for:**
```
lookup monitoring-kube-prometheus-prometheus.monitoring
i/o timeout
```

> This confirms Grafana is failing to resolve the Prometheus service name — pointing to a DNS issue.

---

## 🟦 Step 4 — Verify Prometheus Service and Endpoints

```bash
kubectl get svc -n monitoring
kubectl get endpoints -n monitoring
```

> Prometheus service and endpoints exist — the issue is communication, not the Prometheus application itself.

---

## 🟦 Step 5 — Test DNS from Inside Grafana Pod

```bash
# Get Grafana pod name
kubectl get pods -n monitoring

# Exec into Grafana pod
kubectl exec -it <grafana-pod-name> -n monitoring -- sh
```

Inside the pod:
```sh
nslookup monitoring-kube-prometheus-prometheus.monitoring.svc.cluster.local
```

**Expected (broken state):**
```
connection timed out
```

> DNS resolution is failing from inside the pod — confirms cluster DNS issue.

```sh
exit
```

---

## 🟦 Step 6 — Verify CoreDNS Pods

```bash
kubectl get pods -n kube-system | grep coredns
```

> CoreDNS pods show Running — important lesson: a running pod does not always mean the service is working correctly.

---

## 🟦 Step 7 — Test Kubernetes DNS from Temporary Pod

```bash
kubectl run dns-test --image=busybox:1.36 -it --rm --restart=Never -- sh
```

Inside the pod:
```sh
nslookup kubernetes.default.svc.cluster.local
```

**Expected (broken state):**
```
connection timed out
```

> Even Kubernetes' own API service DNS fails — this confirms the issue is cluster-wide, not just Grafana or Prometheus.

```sh
exit
```

---

## 🟦 Step 8 — Verify CoreDNS Service and Endpoints

```bash
kubectl get svc -n kube-system
kubectl get endpoints -n kube-system kube-dns
```

> CoreDNS service IP is `10.96.0.10` and endpoints exist — yet DNS still fails. Time to investigate networking.

---

## 🟦 Step 9 — Test Pod-to-Pod Connectivity Across Nodes

```bash
kubectl run nettest1 --image=busybox:1.36 -it --rm --restart=Never -- sh
```

Inside the pod:
```sh
# Ping first CoreDNS pod (same node) — should work
ping 10.244.0.2

# Ping second CoreDNS pod (different node) — will fail
ping 10.244.1.5
```

> One pod reachable, another not — **cross-node pod networking is broken.**

```sh
exit
```

---

## 🟦 Step 10 — Check Flannel Node Annotations

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{.metadata.annotations.flannel\.alpha\.coreos\.com/public-ip}{"\n\n"}{end}'

kubectl get nodes -o wide
```

**Example broken output:**
```
k8s-master
192.168.29.230

k8s-worker
192.168.56.116

k8s-worker2
192.168.29.63
```

> Nodes are using different networks. Flannel selected the NAT interface (`192.168.29.x`) on some nodes and the Host-Only interface (`192.168.56.x`) on others — VXLAN tunnels are broken.

---

## 🟦 Step 11 — Root Cause Identified

**Cause:** The cluster VMs have multiple network adapters (NAT + Host-Only). Flannel automatically selected different interfaces on different nodes, breaking cross-node pod communication and cascading into DNS failures and the Grafana plugin error.

---

## 🟦 Step 12 — Apply Fix (Force Flannel Interface)

```bash
kubectl -n kube-flannel edit ds kube-flannel-ds
```

Find the `args:` section under `containers:` and add `--iface=enp0s8`:

```yaml
containers:
- args:
  - --ip-masq
  - --kube-subnet-mgr
  - --iface=enp0s8
```

Save and exit.

> This forces Flannel to use the Host-Only interface (`enp0s8`) on all nodes, ensuring VXLAN tunnels use the same network.

---

## 🟦 Step 13 — Restart Flannel DaemonSet

```bash
kubectl rollout restart ds kube-flannel-ds -n kube-flannel

# Watch pods restart
kubectl get pods -n kube-flannel -w
```

---

## 🟦 Step 14 — Verify Fix

```bash
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\n"}{.metadata.annotations.flannel\.alpha\.coreos\.com/public-ip}{"\n\n"}{end}'
```

**Expected (fixed state):**
```
k8s-master
192.168.56.115

k8s-worker
192.168.56.116

k8s-worker2
192.168.56.117
```

> All nodes are now using the same Host-Only network.

---

## 🟦 Step 15 — Test DNS Again

```bash
kubectl run dns-test \
  --image=busybox:1.36 \
  -it --rm --restart=Never -- sh
```

Inside the pod:
```sh
nslookup kubernetes.default.svc.cluster.local
```

**Expected (fixed state):**
```
Server:    10.96.0.10
Address 1: 10.96.0.10 kube-dns.kube-system.svc.cluster.local
Name:      kubernetes.default.svc.cluster.local
Address 1: 10.96.0.1
```

> DNS resolution is working again.

```sh
exit
```

---

## 🟦 Step 16 — Verify Grafana

Open Grafana in browser and refresh the dashboard.

> Dashboards load successfully — Prometheus is reachable, metrics are available, monitoring stack is fully operational.
