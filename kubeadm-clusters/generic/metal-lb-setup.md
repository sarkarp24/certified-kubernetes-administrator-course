Bare-metal Kubernetes environments (like Multipass VMs) lack built-in cloud load balancers (AWS ELB, GCP LB), so Type: LoadBalancer services remain in <pending> indefinitely. MetalLB acts as an internal network load balancer, assigning IPs from a reserved pool on your local subnet (10.10.0.0/24).  

## 1.Enable Strict ARP in Kube-Proxy:Prerequisite.MetalLB requires strictARP enabled in kube-proxy for Layer 2 ARP mode to function.Open the kube-proxy ConfigMap for editing:
kubectl edit configmap -n kube-system kube-proxy
Find strictARP under ipvs and change it to true:YAMLipvs:
  strictARP: true

## 2.Install MetalLB Manifests:Deployment.Deploy the MetalLB controller and speaker components:

kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.8/config/manifests/metallb-native.yaml

Verification: Run kubectl get pods -n metallb-system and ensure the controller and speaker pods reach Running status.

## 3.Configure IPAddressPool:Address Allocation.Define an IP range within your host subnet (10.10.0.20 to 10.10.0.30) that MetalLB can hand out to LoadBalancer services.Save this manifest as metallb-pool.yaml:

apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: custom-ip-pool
  namespace: metallb-system
spec:
  addresses:
  - 10.10.0.20-10.10.0.30

Apply the configuration:

kubectl apply -f metallb-pool.yaml

## 4.Configure Layer 2 Advertisement:Advertisement.Tell MetalLB to announce the allocated IPs using ARP across your Multipass network bridge.Save this manifest as metallb-l2.yaml:

apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: l2-advertisement
  namespace: metallb-system
spec:
  ipAddressPools:
  - custom-ip-pool

Apply the advertisement:

kubectl apply -f metallb-l2.yaml

## 5.Verify LoadBalancer External IP:Verification.Check your existing LoadBalancer service state:

kubectl get svc

Verification: The EXTERNAL-IP field will transition from <pending> to an assigned IP from your range (e.g., 10.10.0.20).

Your lb-service successfully received the 10.10.0.20 IP from MetalLB, but your Mac browser cannot connect because 10.10.0.20 is bound to the VM's internal virtual network bridge (bridge100), which your Mac host does not automatically route to by default. Additionally, lb-service listens on port 8080, whereas running curl 10.10.0.20 defaults to port 80.

## Step 1: Add the Static Route on Your Mac
Run this command in your Mac Terminal (not inside the Multipass VM) to tell macOS to route 10.10.0.0/24 traffic through the bridge100 interface:

sudo route -n add -net 10.10.0.0/24 -interface bridge100

Verification: Test if your Mac host can reach the load balancer IP on the network layer: