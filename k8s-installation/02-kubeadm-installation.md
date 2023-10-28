# Installing Kubernetes on Ubuntu Linux 18.04 

Pre-Requisites 
1. Set FQDN for Master Node and Work Nodes
2. Swapoff -a
3. Install curl
4. Stop and disable UFW (Firewall)



# All the configuration Done as SUDO User

# Step: 01:  Install Docker on Master and Work Machines.

sudo swapoff -a

sudo apt update

sudo apt install docker.io -y

sudo usermod -aG docker e00049 && newgrp docker

sudo systemctl start docker && sudo systemctl enable docker

# Step 02: Add the Kubernetes signing key on both the Machines

sudo curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add

# Step 03: Add Xenial Kubernetes Repository on both the machines

sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"

 # Step 04: Install Kubeadm on the both the Machines
 
sudo apt -y install vim git curl wget kubelet=1.26.9-00 kubeadm=1.26.9-00 kubectl=1.26.9-00
 
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

.
.
.
Source: https://phoenixnap.com/kb/install-kubernetes-on-ubuntu
Source: containerd instllation - https://www.techrepublic.com/article/install-containerd-ubuntu/

