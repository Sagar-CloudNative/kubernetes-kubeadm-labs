# Lab 08 – Kubernetes etcd Backup & Restore - Commands

This document provides the step-by-step commands used for the **etcd Backup and Restore** lab.

---

## 1️⃣ Confirm Cluster is Healthy

```bash
kubectl get nodes
kubectl get cs
```

## 2️⃣ Inspect etcd Static Pod

```bash
# Verify the etcd pod is running
kubectl get pods -n kube-system | grep etcd

# Identify the data directory location
sudo cat /etc/kubernetes/manifests/etcd.yaml | grep data-dir
```

## 3️⃣ Install etcdctl & Check etcd Health

```bash
# Check etcd version running in cluster
kubectl describe pod etcd-k8s-master -n kube-system | grep Image

# Download etcd binaries
ETCD_VER=v3.5.16
wget https://github.com/etcd-io/etcd/releases/download/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz

# Extract binaries
tar -xvf etcd-${ETCD_VER}-linux-amd64.tar.gz

# Move etcdctl to system path
sudo mv etcd-v3.5.16-linux-amd64/etcdctl /usr/local/bin/
sudo mv etcd-v3.5.16-linux-amd64/etcdutl /usr/local/bin/

# Verify installation
etcdctl version

# Check etcd health
sudo ETCDCTL_API=3 /usr/local/bin/etcdctl \
--endpoints=[https://127.0.0.1:2379](https://127.0.0.1:2379) \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
endpoint health
```

## 4️⃣ Create Test Resources

```bash
# Create namespace
kubectl create ns disaster-test
kubectl get ns

# Create nginx deployment
kubectl create deployment nginx --image=nginx
kubectl get deploy
kubectl get pods -o wide
```

## 5️⃣ Ensure Admin RBAC Exists

```bash
# Check existing admin binding
kubectl get clusterrolebinding | grep kubernetes-admin

# Create admin binding if missing
kubectl create clusterrolebinding kubernetes-admin-binding \
--clusterrole=cluster-admin \
--user=kubernetes-admin

# Verify binding
kubectl get clusterrolebinding | grep kubernetes-admin
```

## 6️⃣ Take Snapshot

```bash
# Create backup directory
sudo mkdir -p /backup

# Take snapshot
sudo ETCDCTL_API=3 /usr/local/bin/etcdctl \
--endpoints=[https://127.0.0.1:2379](https://127.0.0.1:2379) \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
snapshot save /backup/etcd-snapshot.db
```

## 7️⃣ Verify Snapshot

```bash
sudo /usr/local/bin/etcdutl snapshot status /backup/etcd-snapshot.db -w table
```

## 8️⃣ Simulate Disaster

```bash
# Stop kubelet
sudo systemctl stop kubelet
sudo systemctl status kubelet

# Stop etcd container
sudo crictl ps
sudo crictl stop <etcd-container-id>

# Simulate data loss
sudo mv /var/lib/etcd /var/lib/etcd-backup

# Start kubelet again
sudo systemctl start kubelet
sudo systemctl status kubelet

# Check cluster (Expect errors/timeout)
kubectl get pods
kubectl get nodes
```

## 9️⃣ Restore Snapshot

```bash
# Stop API server
sudo mv /etc/kubernetes/manifests/kube-apiserver.yaml /tmp/
sudo crictl ps | grep kube-apiserver

# Restore snapshot to new data directory
sudo ETCDCTL_API=3 /usr/local/bin/etcdutl snapshot restore /backup/etcd-snapshot.db \
--data-dir=/var/lib/etcd-restore

# Update etcd manifest paths
# Change --data-dir and volume mounts in /etc/kubernetes/manifests/etcd.yaml
# Save and exit

# Bring back API server
sudo mv /tmp/kube-apiserver.yaml /etc/kubernetes/manifests/
sudo crictl ps | grep kube-apiserver
```

## 🔟 Validate Recovery

```bash
kubectl get nodes
kubectl get ns
kubectl get deploy
kubectl get clusterrolebinding | grep kubernetes-admin

# Verify etcd health
sudo ETCDCTL_API=3 /usr/local/bin/etcdctl \
--endpoints=[https://127.0.0.1:2379](https://127.0.0.1:2379) \
--cacert=/etc/kubernetes/pki/etcd/ca.crt \
--cert=/etc/kubernetes/pki/etcd/server.crt \
--key=/etc/kubernetes/pki/etcd/server.key \
endpoint health
```

---

## ⚠️ Troubleshooting & Tips
* Always restore to a new directory; never overwrite existing data.
* Ensure API Server is stopped before restore.
* Verify certificates are correct.
* Check etcd endpoint is correct (127.0.0.1:2379).
* Always validate snapshot using `etcdutl snapshot status`.

> [!TIP]
> Take frequent etcd backups in production. Losing etcd without a snapshot can result in total cluster loss.
