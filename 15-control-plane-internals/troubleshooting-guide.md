# 🆘 Emergency Troubleshooting Guide

Use these commands when `kubectl` times out or returns "Connection Refused."

### 1️⃣ Check if the Kubelet is alive
If the Kubelet is down, nothing else will work on this node.
```bash
systemctl status kubelet
# If down:
sudo systemctl start kubelet
```

### 2️⃣ Inspect System Logs
Look for errors in the Kubelet logs to see why it can't start the control plane.
```bash
sudo journalctl -u kubelet -f
```

### 3️⃣ Use `crictl` (The kubectl for containers)
When the API Server is down, `kubectl` cannot talk to the cluster. Use `crictl` to talk directly to the container runtime (Containerd/Docker).
```bash
# List all running control plane components
sudo crictl ps

# Inspect logs of a specific crashed container (e.g., API Server)
# First get the ID from 'crictl ps -a'
sudo crictl logs <container-id>
```

### 4️⃣ Check the Manifests
Ensure the YAML files exist in the correct directory.
```bash
ls -l /etc/kubernetes/manifests/
```
**Common Error:** If a file has a typo or is moved out of this folder, the Kubelet will instantly kill the associated pod.

---

## 🛠️ Typical Failure Scenarios

| Symptom | Common Cause | Fix |
| :--- | :--- | :--- |
| `Connection Refused` | API Server container crashed | Check `crictl ps` and `journalctl` |
| `etcdserver: request timed out` | etcd directory full or disk slow | Check `/var/lib/etcd` space |
| `Partially Ready Nodes` | Certificates expired | Run `kubeadm certs check-expiration` |
