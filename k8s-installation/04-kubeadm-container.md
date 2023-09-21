```bash
# Update the package list and install necessary packages
sudo apt update 
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
sudo add-apt-repository "deb https://apt.kubernetes.io/ kubernetes-xenial main" 
sudo apt update 

# Install Kubernetes components and containerd
sudo apt-get install -y  kubeadm kubelet kubectl containerd 

# Create containerd configuration
sudo mkdir -p /etc/containerd 
sudo chown $USER:$USER /etc/containerd  
sudo containerd config default > /etc/containerd/config.toml 

# Enable necessary kernel modules and settings
sudo modprobe br_netfilter 
sudo sysctl net.ipv4.ip_forward=1 

# Restart and enable containerd service
sudo systemctl restart containerd 
sudo systemctl enable containerd 

# Initialize the Kubernetes cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Copy the Config file 
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Deploy a network plugin (Weave in this case)
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

# Check the status of nodes
kubectl get nodes
