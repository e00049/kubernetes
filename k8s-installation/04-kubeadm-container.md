sudo apt update 
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add - && \
sudo add-apt-repository "deb https://apt.kubernetes.io/ kubernetes-xenial main" 
sudo apt update 
sudo apt-get install -y  kubeadm kubelet kubectl containerd 
sudo mkdir -p /etc/containerd 
sudo chown $USER:$USER /etc/containerd  
sudo containerd config default > /etc/containerd/config.toml 
sudo modprobe br_netfilter 
sudo sysctl net.ipv4.ip_forward=1 
sudo systemctl restart containerd 
sudo systemctl enable containerd 
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
kubectl get nodes
