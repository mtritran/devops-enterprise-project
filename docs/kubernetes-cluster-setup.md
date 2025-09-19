# Kubernetes Cluster Setup

## Overview

This guide sets up a 3-node Kubernetes cluster with high availability masters using kubeadm.

## 1. Initial Server Setup

Run on all 3 servers:

### Add hosts entries:
```bash
echo -e "<master1-private-ip> k8s-master-1\n<master2-private-ip> k8s-master-2\n<master3-private-ip> k8s-master-3" | sudo tee -a /etc/hosts
```

### System updates and user setup:
```bash
sudo apt update -y && sudo apt upgrade -y
adduser devops
visudo
```

Add the following line under root privileges:
```
devops ALL=(ALL:ALL) ALL
```

Switch to devops user:
```bash
su devops
cd
```

## 2. Kubernetes Prerequisites

Run on all 3 servers:

### Disable swap and configure kernel modules:
```bash
sudo swapoff -a
sudo sed -i '/swap.img/s/^/#/' /etc/fstab
echo -e "overlay\nbr_netfilter" | sudo tee /etc/modules-load.d/containerd.conf > /dev/null
sudo modprobe overlay
sudo modprobe br_netfilter
echo "net.bridge.bridge-nf-call-ip6tables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.bridge.bridge-nf-call-iptables = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
echo "net.ipv4.ip_forward = 1" | sudo tee -a /etc/sysctl.d/kubernetes.conf
sudo sysctl --system
```

## 3. Install Container Runtime (containerd)

Run on all 3 servers:

```bash
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update -y
sudo apt install -y containerd.io
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd
sudo systemctl status containerd
```

## 4. Install Kubernetes Components

Run on all 3 servers:

```bash
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
sudo apt update -y
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

## 5. Initialize First Master Node

Run on **k8s-master-1**:

```bash
sudo kubeadm init --control-plane-endpoint "<master1-private-ip>:6443" --upload-certs
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.25.0/manifests/calico.yaml
```

## 6. Join Additional Master Nodes

Run on **k8s-master-2** and **k8s-master-3**:

```bash
sudo kubeadm join <master1-private-ip>:6443 --token <your_token> --discovery-token-ca-cert-hash <your_sha> --control-plane --certificate-key <your_cert>
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## 7. Verify Cluster

Check cluster nodes:
```bash
kubectl get no
```

Expected output:
```
NAME           STATUS   ROLES           AGE   VERSION
k8s-master-1   Ready    control-plane   5m    v1.30.14
k8s-master-2   Ready    control-plane   1m    v1.30.14  
k8s-master-3   Ready    control-plane   1m    v1.30.14
```

## 8. Install Ingress Controller

Run on **k8s-master-1**:

### Install Helm:
```bash
wget https://get.helm.sh/helm-v3.16.2-linux-amd64.tar.gz
tar xvf helm-v3.16.2-linux-amd64.tar.gz
sudo mv linux-amd64/helm /usr/bin/
helm version
```

### Install Ingress NGINX:
```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
helm pull ingress-nginx/ingress-nginx
tar -xzf ingress-nginx-4.11.3.tgz
nano ingress-nginx/values.yaml
```

Edit the values.yaml file:
- Change `type: LoadBalancer` to `type: NodePort`
- Set `nodePort http: "30080"`
- Set `nodePort https: "30443"`

### Deploy Ingress:
```bash
kubectl create ns ingress-nginx
kubectl taint nodes k8s-master-1 node-role.kubernetes.io/control-plane:NoSchedule-
kubectl taint nodes k8s-master-2 node-role.kubernetes.io/control-plane:NoSchedule-
kubectl taint nodes k8s-master-3 node-role.kubernetes.io/control-plane:NoSchedule-
helm -n ingress-nginx install ingress-nginx -f ingress-nginx/values.yaml ingress-nginx
```

### Verify Ingress:
```bash
kubectl get po -n ingress-nginx
```

The Kubernetes cluster is now ready for workload deployment.