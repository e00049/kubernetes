```bash
#!/usr/bin/env bash

sudo useradd -s /bin/bash -d /home/e00049 -m e00049
sudo echo -e "google\ngoogle" | passwd e00049 > /dev/null 2>&1
echo "e00049 ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
sed -i 's/PermitRootLogin forced-commands-only/#PermitRootLogin forced-commands-only/g' /etc/ssh/sshd_config
systemctl restart sshd >/dev/null 2>&1

sudo swapoff -a

sudo tee /etc/modules-load.d/containerd.conf <<EOF
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

sudo tee /etc/sysctl.d/kubernetes.conf<<EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF

sudo sysctl --system
sudo apt update
sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

sudo apt update
sudo apt install -y containerd.io

containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml

sudo systemctl restart containerd && sudo systemctl enable containerd && \
sudo systemctl status containerd

sudo apt install -y apt-transport-https curl 
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/kubernetes-xenial.gpg
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo apt update 
sudo apt -y install vim git curl wget kubelet=1.27.6-00 kubeadm=1.27.6-00 kubectl=1.27.6-00

sudo kubeadm init --pod-network-cidr=10.244.0.0/16 

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml

kubeadm token create --print-join-command 

