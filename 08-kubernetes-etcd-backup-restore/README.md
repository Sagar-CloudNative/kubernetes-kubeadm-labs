# 💾 Lab 08 – Kubernetes etcd Backup & Restore (Disaster Recovery)

---
This lab covers **etcd Backup and Restore** in a kubeadm cluster.  

This is a 3-node setup:  
- 1 Control Plane node  
- 2 Worker nodes  

We will cover:  
- Where etcd runs in kubeadm  
- How to take a proper backup  
- How to simulate a real failure  
- How to restore the cluster safely  

This topic is critical for production environments and CKA exam prep.  

---

## 🔍 Why etcd Backup is Mandatory

In Kubernetes, **etcd stores the full cluster state**:  

- Pods  
- Deployments  
- Services  
- ConfigMaps  
- Secrets  
- RBAC rules  

If etcd data is lost (disk crash, accidental deletion, or control plane failure), **cluster state is gone**.  
> If etcd is gone, the cluster is gone.  

Backup is **mandatory** in production.

---

## 🛠️ Where Does etcd Run in kubeadm?

- Runs **only on Control Plane node**  
- Runs as a **static Pod**: manifest at `/etc/kubernetes/manifests/etcd.yaml`  
- Data stored in `/var/lib/etcd`  

Static Pod is managed by **kubelet**, which ensures it’s always running.  

---

## 🔁 etcd Backup & Restore Workflow

**Backup Phase:**  
1. Check etcd health  
2. Take snapshot with etcdctl  
3. Verify snapshot  

**Restore Phase:**  
4. Simulate disaster  
5. Stop API Server  
6. Restore snapshot to **new data directory**  
7. Update etcd manifest  
8. Control plane restarts  
9. Validate recovery  

**Rule:** Always restore to a **new directory**, never overwrite existing data.
