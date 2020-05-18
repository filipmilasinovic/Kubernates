# Install Kubernetes cluster on Ubuntu 20.04 server

To manualy deploy Kubernetes cluster on physical or virtual machines follow this procesure:

## Pre-requisites
Two or more physical or virtual machines with network card and Ubuntu 20.04 installed. One machine for Kubernetes Master and at least one for Kubernetes node.
- Kubernates master must have at least 2 CPUs (at least 2 CPU cores, not 2 physical CPUs) and 2GB RAM.
- Nodes can be as small as 1 CPU and 1GBof RAM.

## Installing Kubernetes Master
First update the machine:
```
sudo apt-get update && sudo apt-get upgrade -y
```

### Install Docker
```
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### Install Kubernetes
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo apt-get install kubeadm kubelet kubectl -y
```

### Set Hostname and hosts file
```
sudo hostnamectl set-hostname kubemaster

cat >>/etc/hosts<<EOF
192.198.10.100 kubemaster.example.com kubemaster
192.198.10.101 kubenode.example.com kubenode1
192.198.10.102 kubenode.example.com kubenode2
EOF
```

### Turn swap off
```
sudo nano /etc/fstab
comment out line with swapfile
sudo swapoff -a
```

### Initialise pod network
Now on the master only, issue the command without root privileges:
```
kubeadm init --pod-network-cidr=192.168.10.100/24
```

### Copy Kubernetes config
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
### Deploy pod network
```
sudo kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

## Installing Kubernetes Node
First update the machine:
```
sudo apt-get update && sudo apt-get upgrade -y
```

### Install Docker
```
sudo apt-get install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
```

### Install Kubernetes
```
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
sudo apt-get install kubeadm kubelet kubectl -y
```

### Set Hostname and hosts file
```
sudo hostnamectl set-hostname kubenode1

cat >>/etc/hosts<<EOF
192.198.10.100 kubemaster.example.com kubemaster
192.198.10.101 kubenode.example.com kubenode1
192.198.10.102 kubenode.example.com kubenode2
EOF
```

### Turn swap off
```
sudo nano /etc/fstab
comment out line with swapfile
sudo swapoff -a
```
