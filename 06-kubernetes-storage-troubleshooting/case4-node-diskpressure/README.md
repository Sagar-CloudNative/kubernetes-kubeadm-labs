# ❌ Case 4 – Node DiskPressure

Sometimes storage failures are caused by **node resource pressure**, not YAML mistakes.

DiskPressure means the node disk is nearly full.

When this happens:

- kubelet marks `DiskPressure=True`
- scheduler avoids that node

---

## Step 1 – Verify Node State

```
kubectl get nodes
kubectl describe node k8s-worker | grep -i diskpressure
```

Expected:

```
DiskPressure False
```

---

## Step 2 – Simulate Disk Pressure

SSH into worker node.

```
ssh k8s-worker
sudo fallocate -l 35G /bigfile
df -h
```

Restart kubelet.

```
sudo systemctl restart kubelet
```

Check again.

```
kubectl describe node k8s-worker | grep -i diskpressure
```

Expected:

```
DiskPressure True
```

---

## Step 3 – Create Test Pod

```
kubectl run test-pod --image=nginx
kubectl get pods -o wide
```

Scheduler will use the healthy node.

---

## Step 4 – Simulate Full Scheduling Failure

Cordon the healthy node.

```
kubectl cordon k8s-worker2
```

Create pod again.

```
kubectl run test-pod --image=nginx
```

Pod remains **Pending**.

---

## Cleanup

Remove large file.

```
ssh k8s-worker
sudo rm -f /bigfile
sudo systemctl restart kubelet
```

Uncordon node.

```
kubectl uncordon k8s-worker2
```

Delete pod.

```
kubectl delete pod test-pod --force --grace-period=0
```
