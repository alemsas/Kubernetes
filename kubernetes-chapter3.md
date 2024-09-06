# Chapter 3: Setting Up a Kubernetes Cluster

## 3.1 Cluster Configuration

We'll be setting up a Kubernetes cluster with the following nodes:
- Master: 172.31.60.117
- Worker 1: 172.31.60.118
- Worker 2: 172.31.60.119

## 3.2 Prerequisites

Ensure all nodes meet these requirements:
- Ubuntu 20.04 LTS or later
- At least 2 CPU cores
- At least 2GB RAM
- Unique hostname, MAC address, and product_uuid for every node
- Certain ports are open (we'll cover this in the firewall setup)

## 3.3 Initial Setup (All Nodes)

Run these commands on all nodes:

```bash
# Update the system
sudo apt update && sudo apt upgrade -y

# Install necessary packages
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

# Add Kubernetes apt repository
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update apt and install kubelet, kubeadm, and kubectl
sudo apt update
sudo apt install -y kubelet kubeadm kubectl

# Hold these packages at their current version
sudo apt-mark hold kubelet kubeadm kubectl

# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Load necessary modules
sudo modprobe overlay
sudo modprobe br_netfilter

# Set up required sysctl params
sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

# Reload sysctl
sudo sysctl --system

# Install containerd
sudo apt install -y containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
```

## 3.4 Initialize the Master Node

On the master node (172.31.60.117), run:

```bash
# Initialize the cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --apiserver-advertise-address=172.31.60.117

# Set up kubectl for the current user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Install a pod network add-on (we'll use Flannel in this example)
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

Save the `kubeadm join` command that is output after initialization. You'll need this to join worker nodes to the cluster.

## 3.5 Join Worker Nodes

On both worker nodes (172.31.60.118 and 172.31.60.119), run the `kubeadm join` command you saved from the previous step. It will look something like this:

```bash
sudo kubeadm join 172.31.60.117:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

## 3.6 Verify Cluster Setup

On the master node, run:

```bash
kubectl get nodes
```

You should see all three nodes listed and in the 'Ready' state.

## 3.7 Configure Firewall (if needed)

If you're using a firewall, ensure these ports are open:

Master node:
- TCP 6443: Kubernetes API server
- TCP 2379-2380: etcd server client API
- TCP 10250: Kubelet API
- TCP 10251: kube-scheduler
- TCP 10252: kube-controller-manager

Worker nodes:
- TCP 10250: Kubelet API
- TCP 30000-32767: NodePort Services

## 3.8 Post-Installation Steps

1. Set up dashboard (optional):
   ```bash
   kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.5.0/aio/deploy/recommended.yaml
   ```

2. Create a sample deployment:
   ```bash
   kubectl create deployment nginx --image=nginx
   kubectl expose deployment nginx --port=80 --type=NodePort
   ```

3. Verify the deployment:
   ```bash
   kubectl get pods
   kubectl get services
   ```

Congratulations! You now have a functioning Kubernetes cluster.
