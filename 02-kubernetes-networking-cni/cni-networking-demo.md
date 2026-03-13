This contains the **hands-on validation from your script**.

```markdown
# CNI Networking Validation Demo

This lab validates that Kubernetes networking is functioning correctly using the **Flannel CNI plugin**.

---

# Step 1 — Verify CNI Plugin

Check the Flannel pods.

```bash
kubectl get pods -n kube-flannel

You should see one Flannel pod running on each node.

Example:

kube-flannel-ds-xxxxx   Running
kube-flannel-ds-yyyyy   Running

This confirms:

CNI plugin is installed

Networking is enabled across nodes

Step 2 — Verify Node Status

Check node status.

kubectl get nodes

Example output:

NAME         STATUS   ROLES           AGE
k8s-master   Ready    control-plane   20m
k8s-worker   Ready    <none>          15m

Nodes remain NotReady until a CNI plugin is installed.

Step 3 — Verify System Pods
kubectl get pods -n kube-system

Example:

coredns-xxxxx    Running
kube-proxy       Running

CoreDNS requires networking to function.

Without CNI it would remain Pending.

Step 4 — Create Test Pods

Create an NGINX pod.

kubectl run pod-a --image=nginx --restart=Never

Create a BusyBox pod for testing.

kubectl run pod-b --image=busybox --restart=Never -- sleep 3600
Step 5 — Check Pod IPs
kubectl get pods -o wide

Example:

NAME    READY   STATUS   IP           NODE
pod-a   1/1     Running  10.244.1.2   k8s-worker
pod-b   1/1     Running  10.244.1.3   k8s-worker

Each Pod receives its own cluster IP address.

Step 6 — Test Pod-to-Pod Communication

Get the IP of Pod A.

kubectl get pod pod-a -o wide

Enter Pod B.

kubectl exec -it pod-b -- sh

Ping Pod A.

ping <POD-A-IP>

Example output:

64 bytes from 10.244.1.2: seq=0 ttl=64 time=1.1 ms
64 bytes from 10.244.1.2: seq=1 ttl=64 time=0.06 ms

Successful ping confirms:

Pod networking works

CNI is functioning correctly

Exit the pod.

exit
Result

You have successfully validated:

CNI installation

Node readiness

Pod IP assignment

Pod-to-Pod communication


---

# After Adding This

Your repo becomes:


kubernetes-kubeadm-labs
│
├── 01-kubeadm-cluster-setup
│
└── 02-kubernetes-networking-cni
├── README.md
├── commands.md
├── cni-networking-demo.md
└── images/


This is **exactly how Kubernetes training labs are structured**.

---

✅ Next video (very likely in your flow) will be something like:


03-multi-node-cluster-expansion


because your script mentions **adding more worker nodes in the next video**.

---

If you want, I can also show you a **very small GitHub improvement (used by DevOps courses)** that will make your repo **look like a full Kubernetes course instead of just a code repo**.
