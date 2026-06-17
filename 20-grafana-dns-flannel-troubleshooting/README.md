# Lab 20 — Fix Grafana Plugin Error in Kubernetes | DNS, CoreDNS & Flannel Troubleshooting

[![Kubernetes](https://img.shields.io/badge/Kubernetes-Labs-blue)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs)
[![License](https://img.shields.io/badge/License-MIT-green)](../LICENSE)
[![YouTube](https://img.shields.io/badge/YouTube-Subscribe-red?logo=youtube)](https://www.youtube.com/@TechOpsTutorials)

> **Part of the [Kubernetes Real-World Labs (kubeadm)](https://github.com/Sagar-CloudNative/kubernetes-kubeadm-labs) series**

---

## 🎯 What You'll Learn

- How to troubleshoot Grafana plugin errors in Kubernetes
- How to identify DNS failures inside pods using `nslookup`
- How CoreDNS and Flannel interact in a multi-node cluster
- Why cross-node pod networking breaks when Flannel picks the wrong interface
- How to fix Flannel interface configuration and restore full cluster networking

---

## 🧠 Root Cause Summary

| Layer | Symptom | Actual Cause |
|-------|---------|--------------|
| Grafana | Plugin error, dashboards not loading | Cannot reach Prometheus |
| Prometheus | Appears healthy | DNS resolution failing |
| CoreDNS | Pods running | Cross-node pod connectivity broken |
| Flannel | Seemingly OK | Selected different network interfaces on different nodes |

**Root cause:** Flannel used the NAT interface (`192.168.29.x`) on some nodes and the Host-Only interface (`192.168.56.x`) on others. This broke VXLAN tunnels between nodes, causing cross-node pod communication to fail — which cascaded into DNS failures and eventually the Grafana plugin error.

---

## 🧠 Key Troubleshooting Lesson

> A **running pod does not always mean the service is functioning correctly.**

Always follow a structured approach:
1. Check application logs for actual errors
2. Validate service and endpoint availability
3. Test DNS resolution from inside pods
4. Test pod-to-pod connectivity across nodes
5. Investigate networking layer (CNI/Flannel)

---

## 🗂️ Files in This Lab

| File | Description |
|------|-------------|
| `commands.md` | Full troubleshooting and fix commands step by step |

---

## 🚀 Troubleshooting Flow

| Step | Action | Finding |
|------|--------|---------|
| 1 | Check Grafana UI | Plugin error, dashboards not loading |
| 2 | Verify monitoring pods | All pods Running — no obvious issue |
| 3 | Check Grafana logs | DNS `i/o timeout` connecting to Prometheus |
| 4 | Verify Prometheus service and endpoints | Service healthy |
| 5 | Test DNS from inside Grafana pod | `nslookup` times out — DNS broken |
| 6 | Check CoreDNS pods | Running — but still failing |
| 7 | Test Kubernetes DNS from temp pod | `kubernetes.default` resolution fails — cluster-wide DNS broken |
| 8 | Verify CoreDNS service and endpoints | Service and endpoints exist |
| 9 | Test pod-to-pod connectivity | Cross-node pod ping fails — networking broken |
| 10 | Check Flannel node annotations | Different IPs used per node — wrong interface selected |
| 11 | Identify root cause | Flannel selected NAT interface on some nodes |
| 12 | Apply fix | Force Flannel to use `--iface=enp0s8` |
| 13 | Restart Flannel DaemonSet | Networking rebuilt with correct interface |
| 14 | Verify fix | All nodes now using `192.168.56.x` |
| 15 | Test DNS again | Resolution working |
| 16 | Verify Grafana | Dashboards loading successfully |

---

## ⚠️ Environment Note

This issue is specific to clusters with **multiple network adapters** — for example, VirtualBox VMs with both a NAT adapter and a Host-Only adapter. Flannel automatically selects an interface, and if it picks different ones on different nodes, cross-node networking breaks.

**Fix:** Force Flannel to use a specific interface using `--iface=<interface-name>`.

---

## ▶️ Watch the Video

👉 [**Grafana DNS Flannel Troubleshooting — YouTube**](https://youtu.be/ph6tQmMfUvM?si=avhWOKJgqIJg4yPN)

---

## ⬅️ Previous Lab

[Lab 19 — Monitor Kubernetes with Prometheus Using Helm](../19-kubernetes-prometheus-monitoring)
