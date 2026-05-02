# 🖥️ Lab 07 – Kubernetes LoadBalancer on Bare Metal using MetalLB

In this lab, we simulate a **real LoadBalancer** in a bare-metal kubeadm cluster using **MetalLB**.  

By the end, your service will have an external IP — just like in cloud environments.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 07 Tutorial**](https://youtu.be/QDl6I5tGn8s?si=zc-gKm009FBtH4to)

---

## 🎬 Prerequisites – VirtualBox Settings

Before starting, ensure the following **VirtualBox adapter configuration**:

| Adapter   | Type              | Interface | Promiscuous Mode | Notes |
|-----------|-----------------|-----------|-----------------|-------|
| Adapter 1 | Bridged Adapter   | enp0s3   | Allow All       | Optional. Provides internet access. |
| Adapter 2 | Host-Only Adapter | enp0s8   | Allow All       | Required for MetalLB. Assign static IPs here. |

Verify network interfaces:

```bash
ip a
```

Check cluster nodes:

```bash
kubectl get nodes -o wide
```

---

## ⚡ Step 0 – Enable strictARP in kube-proxy

Edit the kube-proxy ConfigMap:

```bash
kubectl edit configmap -n kube-system kube-proxy
```

Set:

```yaml
strictARP: true
```

Restart kube-proxy pods:

```bash
kubectl rollout restart daemonset kube-proxy -n kube-system
```

This ensures MetalLB can announce IPs correctly in Layer 2 mode.

---

## 🎬 Step 1 – Install MetalLB

Apply MetalLB manifests:

```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml
```

Verify pods:

```bash
kubectl get pods -n metallb-system
```

You should see one controller pod and speaker pods running on each node.

---

## 🎬 Step 2 – Configure IP Address Pool

Create `metallb-ip-pool.yaml`:

```yaml
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: my-ip-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.56.240-192.168.56.250
```

Apply the configuration:

```bash
kubectl apply -f metallb-ip-pool.yaml
```

> Ensure IP range matches your host-only network and is not in use by other devices.

---

## 🎬 Step 3 – Create L2 Advertisement

Create `l2-advertisement.yaml`:

```yaml
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - my-ip-pool
  interfaces:
  - enp0s8
```

Apply:

```bash
kubectl apply -f l2-advertisement.yaml
```

---

## 🎬 Step 4 – Deploy Test Application (NGINX)

Create a deployment:

```bash
kubectl create deployment nginx-demo --image=nginx
```

Expose it as a LoadBalancer service:

```bash
kubectl expose deployment nginx-demo --port=80 --type=LoadBalancer
kubectl get svc nginx-demo
```

You should see an **External IP** assigned by MetalLB.

Check details:

```bash
kubectl describe svc nginx-demo
```

---

## 🎬 Step 5 – Access the Service Externally

From your host:

```bash
curl http://<EXTERNAL-IP>
```

Or open the browser:

```
http://<EXTERNAL-IP>/
```

You should see the default **NGINX welcome page**.

---

## 🛠️ Troubleshooting – Host Unreachable

If the external IP works inside the cluster but not from host:

```cmd
arp -d <EXTERNAL-IP>
```

This clears stale ARP entries and forces the host to resolve the IP again.

---

## 🔁 Cleanup Lab 07

Delete test application:

```bash
kubectl delete svc nginx-demo
kubectl delete deployment nginx-demo
```

Remove MetalLB configuration:

```bash
kubectl delete ipaddresspool my-ip-pool -n metallb-system
kubectl delete l2advertisement l2-advertisement -n metallb-system
```

Optional: Remove MetalLB completely:

```bash
kubectl delete -f https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml
kubectl get pods -n metallb-system
```

---

## 🔗 References

* [MetalLB Official Documentation](https://metallb.universe.tf/)
* [Kubernetes Services Overview](https://kubernetes.io/docs/concepts/services-networking/service/)

---

[⬅️ Previous](../06-kubernetes-storage-troubleshooting) | [🏠 Home](../README.md) | [Next Lab: etcd Backup & Restore ➡️](../08-kubernetes-etcd-backup-restore)
