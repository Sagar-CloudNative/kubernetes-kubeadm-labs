# 🔧 Lab 06: Kubernetes Storage Troubleshooting (Real-World Scenarios)

Storage issues are one of the most common problems in real Kubernetes environments.

Pods may get stuck in **ContainerCreating**, PVCs remain in **Pending**, or sometimes the **node itself becomes unhealthy**.

In this lab we intentionally **break Kubernetes storage configurations** and debug them step-by-step.

---

## 🎥 Video Tutorial

Watch the step-by-step cluster expansion guide here:

👉 [**Watch the Lab 06 Tutorial**](https://youtu.be/6Tl8A9IF7Rs?si=49cYf649N5eSYHCx)


---

## 📚 Troubleshooting Scenarios

| Case | Scenario |
|-----|---------|
| 1 | PVC stuck in Pending due to wrong StorageClass |
| 2 | AccessMode mismatch between PV and PVC |
| 3 | Pod stuck in ContainerCreating due to mount failure |
| 4 | Node DiskPressure affecting scheduling |

Each case represents **real-world production failures** and common **CKA troubleshooting scenarios**.

---

## 📂 Lab Structure

```
case1-pvc-pending
case2-accessmode-mismatch
case3-mount-failure
case4-node-diskpressure
```

Run each scenario independently.

---

## 🎯 Learning Goals

After completing this lab you will understand:

- How to debug **PVC Pending issues**
- How **PV and PVC binding rules** work
- How to identify **mount stage failures**
- How **node conditions like DiskPressure** impact scheduling

---

[⬅️ Previous](../05-kubernetes-persistent-storage) | [🏠 Home](../README.md) | [Next Lab: MetalLB LoadBalancer ➡️](../07-kubernetes-loadbalancer-metallb)
