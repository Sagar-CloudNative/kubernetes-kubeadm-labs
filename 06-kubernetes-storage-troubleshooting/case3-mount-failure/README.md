# ❌ Case 3 – Pod Stuck in ContainerCreating

This scenario demonstrates a **mount-stage failure**.

PVC will bind successfully but the **volume mount fails on the node**.

---

## Step 1 – Create Broken PV

```
kubectl apply -f broken-mount-pv.yaml
```

---

## Step 2 – Create PVC

```
kubectl apply -f broken-mount-pvc.yaml
kubectl get pvc
```

Expected:

```
STATUS: Bound
```

---

## Step 3 – Deploy Pod

```
kubectl apply -f broken-mount-pod.yaml
kubectl get pods -w
```

Pod will stay:

```
ContainerCreating
```

---

## Debug

```
kubectl describe pod broken-mount-pod
```

Events show:

```
hostPath type check failed
```

The directory does not exist on the node.

---

## Optional Fix

Create directory on the worker node.

```
ssh k8s-worker
sudo mkdir -p /data/does-not-exist
```

Pod will move to **Running**.

---

## Cleanup

```
kubectl delete pod broken-mount-pod
kubectl delete pvc broken-mount-pvc
kubectl delete pv broken-mount-pv
```
