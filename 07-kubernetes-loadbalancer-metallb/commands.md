# Lab 07: Kubernetes LoadBalancer on Bare Metal using MetalLB – Commands

This document contains all the commands used to deploy and configure MetalLB to provide external LoadBalancer IPs in a bare-metal Kubernetes environment.

---

## Step 0 – Enable strictARP in kube-proxy

Before installing MetalLB, you must enable `strictARP` in the `kube-proxy` configuration.

```bash
# Edit the configmap
kubectl edit configmap -n kube-system kube-proxy
```
*(Find `strictARP: false` and change it to `strictARP: true`, then save and exit)*

```bash
# Restart kube-proxy to apply changes
kubectl rollout restart daemonset kube-proxy -n kube-system
```

---

## Step 1 – Install MetalLB

Apply the native MetalLB manifest to install the controller and speaker pods.

```bash
kubectl apply -f [https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml](https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml)

# Verify the pods are running
kubectl get pods -n metallb-system
```

---

## Step 2 – Configure an IP Address Pool

Define the range of IP addresses MetalLB is allowed to hand out.

```bash
kubectl apply -f metallb-ip-pool.yaml

# Verify the IP pool was created
kubectl get ipaddresspool -n metallb-system
```

---

## Step 3 – Create Layer 2 Advertisement

Tell MetalLB to announce the IP addresses using Layer 2 protocols (ARP/NDP).

```bash
kubectl apply -f l2-advertisement.yaml

# Verify the advertisement
kubectl get l2advertisement -n metallb-system
```

---

## Step 4 – Deploy a Test NGINX Application

Create a deployment and expose it using a Service of type `LoadBalancer`.

```bash
# Create the deployment
kubectl create deployment nginx-demo --image=nginx

# Check the pods
kubectl get pods

# Expose the deployment as a LoadBalancer
kubectl expose deployment nginx-demo --port=80 --type=LoadBalancer

# View the assigned EXTERNAL-IP
kubectl get svc nginx-demo

# View detailed service information
kubectl describe svc nginx-demo
```

---

## Step 5 – Access the Service Externally

Test the connection to the external IP assigned by MetalLB.

```bash
curl http://<EXTERNAL-IP>
```

---

## Cleanup – Reset Environment

Run these commands to remove the test application and uninstall MetalLB.

```bash
# Delete the application and service
kubectl delete svc nginx-demo
kubectl delete deployment nginx-demo

# Delete MetalLB configurations
kubectl delete ipaddresspool my-ip-pool -n metallb-system
kubectl delete l2advertisement l2-advertisement -n metallb-system

# Uninstall MetalLB
kubectl delete -f [https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml](https://raw.githubusercontent.com/metallb/metallb/main/config/manifests/metallb-native.yaml)

# Verify deletion
kubectl get pods -n metallb-system
```
