# ❌ Case 1 – PVC Stuck in Pending (Wrong StorageClass)

This scenario demonstrates a common issue where a PVC references a **non-existent StorageClass**.

---

## Step 1 – Create Broken PVC

```
kubectl apply -f broken-pvc.yaml
kubectl get pvc
```

Expected:

```
broken-pvc   Pending
```

---

## Step 2 – Debug

```
kubectl describe pvc broken-pvc
```

Look at **Events**.

You will see something like:

```
storageclass.storage.k8s.io "wrong-class" not found
```

Root cause identified.

---

## Step 3 – Fix

Delete the PVC.

```
kubectl delete pvc broken-pvc
```

Check available StorageClasses.

```
kubectl get sc
```

Edit the YAML and replace:

```
storageClassName: wrong-class
```

with

```
storageClassName: local-path
```

Apply again:

```
kubectl apply -f broken-pvc.yaml
```

---

## Step 4 – Trigger Binding

Create the test pod.

```
kubectl apply -f test-pod.yaml
```

Check again:

```
kubectl get pvc
```

PVC should now show:

```
STATUS: Bound
```

---

## Cleanup

```
kubectl delete pod test-pod
kubectl delete pvc broken-pvc
```
