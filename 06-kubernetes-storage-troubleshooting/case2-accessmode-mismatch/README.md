# ❌ Case 2 – Access Mode Mismatch

This scenario demonstrates what happens when **PV and PVC access modes do not match**.

---

## Step 1 – Create PersistentVolume

```
kubectl apply -f pv.yaml
kubectl get pv
```

Expected:

```
mismatch-pv   Available
```

---

## Step 2 – Create PVC

```
kubectl apply -f pvc.yaml
kubectl get pvc
```

PVC remains:

```
Pending
```

---

## Step 3 – Debug

```
kubectl describe pvc mismatch-pvc
```

Kubernetes cannot find a compatible volume.

Why?

PV AccessMode:

```
ReadOnlyMany
```

PVC requested:

```
ReadWriteOnce
```

They must match.

---

## Fix

Edit `pvc.yaml`.

Change access mode to:

```
ReadOnlyMany
```

Recreate PVC.

```
kubectl delete pvc mismatch-pvc
kubectl apply -f pvc.yaml
```

PVC should now become **Bound**.

---

## Cleanup

```
kubectl delete pvc mismatch-pvc
kubectl delete pv mismatch-pv
```
