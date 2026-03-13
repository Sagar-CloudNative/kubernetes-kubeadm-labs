# Lab 05 — Kubernetes Persistent Storage (kubeadm Cluster)
[⬅ Previous Lab](../04-kubernetes-services/README.md) | [🏠 Main Directory](../../README.md) | [Next Lab ➡](../06-kubernetes-configmaps-secrets/README.md)

## Overview

In this lab, we will explore **Kubernetes Persistent Storage** in a **real multi-node kubeadm cluster**.

In earlier labs using **Minikube**, storage behavior appeared simple because everything ran on a **single node**.  
However, in a **multi-node kubeadm cluster**, storage behavior becomes more realistic and involves important concepts like:

- StorageClass
- PersistentVolume (PV)
- PersistentVolumeClaim (PVC)
- Dynamic Provisioning
- Node locality
- Access modes
- Reclaim policies

These concepts apply not only to kubeadm clusters but also to **cloud-managed Kubernetes platforms such as EKS, AKS, and GKE**.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 05 Tutorial**](https://youtu.be/jjnVndmsKkY?si=CufsuT7OZtvxpLGu)

---

# Lab Objectives

By completing this lab, you will learn:

- Why kubeadm clusters do not include a default StorageClass
- How to install a dynamic storage provisioner
- How Kubernetes dynamically creates PersistentVolumes
- How PVC binding works
- How persistent storage behaves when pods are deleted
- Why **ReadWriteOnce means "one node", not "one pod"**
- How reclaim policies control the storage lifecycle

---

# Lab Architecture

Example cluster setup:

Control Plane Node  
│  
├── Worker Node 1  
└── Worker Node 2  

We will install a **dynamic storage provisioner** and observe how storage behaves across nodes.

---

# Step 1 — Verify the Cluster

Check cluster nodes.

```bash
kubectl get nodes
```

Check system pods.

```bash
kubectl get pods -A
```

---

# Step 2 — Check StorageClasses

Check existing StorageClasses.

```bash
kubectl get sc
```

Expected result in kubeadm cluster:

```
No resources found
```

This is normal.

Unlike **Minikube**, kubeadm clusters **do not include a default StorageClass**.

---

# Step 3 — Install Local Path Provisioner

For lab environments we install **Rancher Local Path Provisioner**.

```bash
kubectl apply -f https://raw.githubusercontent.com/rancher/local-path-provisioner/master/deploy/local-path-storage.yaml
```

Verify the provisioner pod.

```bash
kubectl get pods -n local-path-storage
```

Check StorageClass.

```bash
kubectl get sc
```

Example output:

```
NAME         PROVISIONER             RECLAIMPOLICY   VOLUMEBINDINGMODE
local-path   rancher.io/local-path   Delete          WaitForFirstConsumer
```

Important field:

```
VolumeBindingMode: WaitForFirstConsumer
```

This means the volume will only be created **when a Pod starts using the claim**.

---

# Step 4 — Create PersistentVolumeClaim

Create the file:

```
pvc.yaml
```

Apply it:

```bash
kubectl apply -f pvc.yaml
```

Check status.

```bash
kubectl get pvc
```

Expected:

```
STATUS: Pending
```

Check PVs.

```bash
kubectl get pv
```

No PV exists yet because the StorageClass uses **WaitForFirstConsumer**.

---

# Step 5 — Deploy Pod Using PVC

Create the pod manifest.

```
pod.yaml
```

Apply it.

```bash
kubectl apply -f pod.yaml
```

Check scheduling.

```bash
kubectl get pods -o wide
```

---

# Step 6 — Observe Dynamic Provisioning

Check PVC.

```bash
kubectl get pvc
```

Now the status should be:

```
Bound
```

Check PV.

```bash
kubectl get pv
```

A **PersistentVolume will be created automatically**.

This is **dynamic provisioning**.

---

# Step 7 — Write Data to the Volume

Enter the pod.

```bash
kubectl exec -it storage-pod -- sh
```

Inside the container:

```bash
cd /data
echo "Hello from kubeadm cluster" > file.txt
cat file.txt
```

Exit container.

```bash
exit
```

---

# Step 8 — Verify Persistence

Delete the pod.

```bash
kubectl delete pod storage-pod
```

Recreate the pod.

```bash
kubectl apply -f pod.yaml
```

Check the file again.

```bash
kubectl exec -it storage-pod -- cat /data/file.txt
```

The file still exists.

This proves **PersistentVolumes survive pod deletion**.

---

# Step 9 — Demonstrate ReadWriteOnce Behavior

Copy the pod manifest.

```bash
cp pod.yaml pod2.yaml
```

Edit the name inside `pod2.yaml`.

```
storage-pod-2
```

Apply it.

```bash
kubectl apply -f pod2.yaml
```

Check scheduling.

```bash
kubectl get pods -o wide
```

If both pods run on the **same node**, they can share the volume.

Important concept:

```
ReadWriteOnce = One Node
NOT One Pod
```

---

# Step 10 — Force Pod to Another Node

Delete the second pod.

```bash
kubectl delete pod storage-pod-2
```

Edit `pod2.yaml` and add a nodeSelector.

```yaml
spec:
  nodeSelector:
    kubernetes.io/hostname: k8s-worker2
```

Apply again.

```bash
kubectl apply -f pod2.yaml
```

The pod may remain in:

```
Pending
```

Check details.

```bash
kubectl describe pod storage-pod-2
```

Possible event:

```
volume node affinity conflict
```

This happens because the **local volume exists only on one node**.

---

# Step 11 — Delete PVC

Delete the PVC.

```bash
kubectl delete pvc demo-pvc
```

If pods are still using it, it will stay in:

```
Terminating
```

Delete the pods.

```bash
kubectl delete pod storage-pod storage-pod-2
```

Check again.

```bash
kubectl get pvc
kubectl get pv
```

If reclaim policy is **Delete**, the PV will be removed automatically.

---

# Key Takeaways

- kubeadm clusters do not include default StorageClasses
- Dynamic provisioning requires a storage provisioner
- PVC requests storage
- StorageClass provisions volumes automatically
- ReadWriteOnce limits mounting to **one node**
- PersistentVolumes outlive pods
- Reclaim policies control storage cleanup

---

# Lab Completed

You have successfully explored **persistent storage in a real multi-node Kubernetes cluster**.

---

## Lab Navigation

| Previous Lab | Main Directory | Next Lab |
|:-------------|:--------------:|---------:|
| [« Lab 04: Kubernetes Services](../04-kubernetes-services/README.md) | [Main Directory](../../README.md) | [Lab 06: Kubernetes ConfigMaps & Secrets »](../06-kubernetes-configmaps-secrets/README.md) |

---
