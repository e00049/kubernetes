# Installing Kubernetes on Ubuntu Linux 18.04 

Pre-Requisites 
1. Set FQDN for Master Node and Work Nodes
2. Swapoff -a
3. Install curl
4. Stop and disable UFW (Firewall)


# All the configuration Done as SUDO User

# Step: 01:  Install Docker on Master and Work Machines.

sudo swapoff -a && \
sudo apt update && \
sudo apt install docker.io -y && \
sudo usermod -aG docker e00049 && newgrp docker && \
sudo systemctl start docker && sudo systemctl enable docker

# Step 02: Install Kuberntes using PIP Package manager

sudo apt-get update && \
sudo apt install python3-pip -y && \
pip install kubernetes && \
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl


# Step 03: Configure repo for kubernetes

sudo apt-get install -y apt-transport-https ca-certificates curl && \
sudo mkdir -p -m 755 /etc/apt/keyrings && \
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
sudo chmod 644 /etc/apt/keyrings/kubernetes-apt-keyring.gpg && \
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list && \
sudo chmod 644 /etc/apt/sources.list.d/kubernetes.list  && \
sudo apt-get update 

 # Step 04: Install Kubeadm on the both the Machines
 
sudo apt-get install kubelet kubeadm kubectl -y 
 
 kubeadm version
 
 # Step 05: Initialize Kubernetes on the master node
 
 sudo kubeadm init --pod-network-cidr=10.244.0.0/16
 
 
 mkdir -p $HOME/.kube
 
 sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
 
 sudo chown  $(id -u):$(id -g) $HOME/.kube/config
 
 # Step 06: RUN this on Master machine and OUTPUT past on worker Nodes with SUDO user mode
 
 sudo kubeadm token create --print-join-command
 
 kubectl get nodes
 
 # Step 07: Deploy a Pod Network through the master node
 
  kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

  
 # To apply Tag for worker
 sudo kubectl label node worker.example.com node-role.kubernetes.io/worker=worker


