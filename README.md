
SA II Project: Building and Scaling expressjs-mongodb-app on Kubernetes
Project Overview
Welcome to the documentation of the System Administration II course at TSBE. This project focuses on the installation and scaling of the expressjs-mongodb-app on a Kubernetes cluster.

Project Goals
The primary objectives of this project are to set up and configure a Kubernetes cluster with the following key features:

Scalability: Utilizing Kubernetes orchestration for seamless scaling.
Persistent Storage: Implementing containers with persistent storage, specifically for a MongoDB Database.
Container Networking: Establishing effective container networking with port forwarding to enable external access.
Security: Configuring a secure environment to minimize potential security risks.
Used Ports:
3000: expressjs-app
27017: MongoDB

Platform & Limitations
The following virtual platform is hosted by the school to execute the project:

vmKL1
The vmKL1 is the management machine used to set up, administer, and access the Kubernetes cluster.

VM Specs:

OS: Kali GNU/Linux
Kernel: Linux 6.5.0-kali3-amd64
CPU: 2 vCPUs
RAM: 16 GB
Storage: /dev/sda 35 GB
Network: Access to vmLM1 & Internet
IP: 192.168.110.70
Installed Software:

vscode
vmLM1

The vmLM1 is the Kubernetes host itself. It will act as Control Plane (Master Node) & Data Plane (Worker Node).
VM Specs:
OS: Ubuntu 22.04.3 LTS
Kernel: Linux
CPU: 4 vCPUs
RAM: 12 GB
Storage: /dev/sda 16 GB
Network: Access to vmKL1 & Internet
IP: 192.168.110.60
Installed Software:
minikube version: v1.32.0
The container network is set up in a way that only necessary communication is allowed. UFW is active and configured. Open ports to the Kubernetes Node (vmLM1):

22/TCP -> SSH
443/TCP -> Kubernetes API
30703/TCP -> express-mongodb-app
8001/TCP -> Kubernetes Dashboard
31185/TCP -> Argocd (optional)
As far as the container network goes, only pods that need external access are configured with NodePort. All other pods are configured with Cluster IP for Kubernetes internal-only communication.

Manual Setup Guide
vmKL1:
# Update the System
sudo apt update -y
sudo apt upgrade -y

vmLM1:
# Port forward , Enable & open necessary ports on the server firewall
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw allow 80
sudo ufw allow 8001 # for minikube dashboard
sudo ufw allow 30703 # for express-mongodb-app
sudo ufw allow 31185 # for argocd (optional)
sudo ufw enable

# Enable SSH access by uncommenting the following line in /etc/ssh/sshd_config
Port 22

# Restart sshd
sudo systemctl restart sshd

# Update the System
sudo apt update -y
sudo apt upgrade -y

# Install Docker
sudo apt install docker.io
docker --version # Check Docker version

# Download Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version # Check Minikube version

# Install Kubectl
sudo snap install kubectl --classic
kubectl version # Check Kubectl version

# Start Minikube with Docker driver
minikube start --driver=docker
sudo usermod -aG docker $USER && newgrp docker
minikube node add —worker
kubectl label node <node_name> node-role.kubernetes.io/worker=worker
kubectl get nodes 

# Update local apt cache
sudo apt update

# Create a namespace for the app
kubectl create namespace nodejs-mongodb-app

# Create a project folder for deployment
$ mkdir ~/kube