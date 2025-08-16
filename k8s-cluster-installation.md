## Kubernetes Cluster Installation and Configuration Guide
Step-by-step guide to set up a multi-node Kubernetes cluster from scratch.  
Covers control plane initialization, worker node joining, pod networking with Flannel, and cluster verification.

## Step 1: Install Dependencies & Container Runtime (Both Control Plane and Worker Nodes)

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg

# Containerd
wget https://github.com/containerd/containerd/releases/download/v2.0.0/containerd-2.0.0-linux-amd64.tar.gz
sudo tar Cxzvf /usr/local containerd-2.0.0-linux-amd64.tar.gz
wget https://raw.githubusercontent.com/containerd/containerd/main/containerd.service
sudo mkdir -p /usr/local/lib/systemd/system
sudo mv containerd.service /usr/local/lib/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now containerd
systemctl status containerd

```

```bash
# runc
wget https://github.com/opencontainers/runc/releases/download/v1.2.1/runc.amd64
sudo install -m 755 runc.amd64 /usr/local/sbin/runc
runc --version
```

```bash
# CNI plugins
wget https://github.com/containernetworking/plugins/releases/download/v1.4.1/cni-plugins-linux-amd64-v1.4.1.tgz
sudo mkdir -p /opt/cni/bin
sudo tar Cxzvf /opt/cni/bin cni-plugins-linux-amd64-v1.4.1.tgz
```

## Step 2: Install Kubernetes Tools (Both Control Plane and Worker Nodes)
```bash
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.33/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.33/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
sudo systemctl enable --now kubelet
```

## Step 3: Disable Swap & Configure Kernel Modules (Both Control Plane and Worker Nodes)
```bash
sudo swapoff -a
sudo sed -i '/ swap / s/^/#/' /etc/fstab
```

```bash
# Load br_netfilter module
sudo modprobe br_netfilter
echo "br_netfilter" | sudo tee /etc/modules-load.d/br_netfilter.conf

# Enable packet forwarding
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/99-kubernetes-cri.conf
echo "net.bridge.bridge-nf-call-ip6tables = 1" | sudo tee -a /etc/sysctl.d/99-kubernetes-cri.conf

# Apply sysctl settings
sudo sysctl --system
```
## Step 4: Initialize Control Plane (Control Plane Node Only)
```bash
sudo kubeadm init --apiserver-advertise-address=$IP --pod-network-cidr=10.244.0.0/16
```
```bash
# Configure kubectl for the current user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Step 5: Apply Pod Networking (Control Plane Node Only)

```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
kubectl get pods -n kube-flannel
```

## Step 6: Join Worker Nodes (Worker Nodes Only)

```bash
# Run this on each worker node (replace placeholders with actual values)
sudo kubeadm join <CONTROL_PLANE_IP>:6443 --token <TOKEN> \
    --discovery-token-ca-cert-hash sha256:<HASH>
```

## Step 7: Verification (Control Plane Node)

```bash
# Check node status
kubectl get nodes -o wide

# Check system pods
kubectl get pods -n kube-system
```

## Helpful Documentation
If something doesn’t work as intended or you want to explore further, these official resources can help:
1) https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/
2) https://containerd.io/downloads/
3) https://github.com/containerd/containerd/blob/main/docs/getting-started.md
