# SA II Project: Building and Scaling expressjs-mongodb-app on Kubernetes

## Project Overview
Welcome to the documentation of the System Administration II course at TSBE. This project focuses on the installation and scaling of the expressjs-mongodb-app on a Kubernetes cluster.

## Project Goals
The primary objectives of this project are to set up and configure a Kubernetes cluster with the following key features:

- **Scalability:** to setup Horizontal and V Pod Autoscaling in kubernetes.
- **Persistent Storage:** Implementing containers with persistent storage, specifically for a MongoDB Database.
- **Container Networking:** Establishing effective container networking with port forwarding to enable external access.
- **Security:** Configuring a secure environment to minimize potential security risks.

Project Display
![project display](kube\project-image\project-display.png)
This application is a note-taking system developed using Express.js and MongoDB. It enables users to create and share textual notes through a web interface and has been Dockerized and the Docker image is available on [Docker Hub](https://hub.docker.com/repository/docker/yavuzozbay/nodeserver). 

```bash
docker pull yavuzozbay/nodeserver:2.0.0
``````
Browser Request Flow through the K8s components
![project display](kube\project-image\architecture-project.jpeg)

MongoDB with StatefulSets on this Project
![project display](kube\project-image\StatefulSet-MongoDB.png)

## Platform & Limitations
The following virtual platform is hosted by the school to execute the project:

<h5>vmKL1</h5>
The vmKL1 is the management machine used to set up, administer, and access the Kubernetes cluster.

**VM Specs:**
- OS: Kali GNU/Linux
- Kernel: Linux 6.5.0-kali3-amd64
- CPU: 2 vCPUs
- RAM: 16 GB
- Storage: /dev/sda 35 GB
- Network: Access to vmLM1 & Internet
- IP: 192.168.110.70

**Installed Software:**
- vscode 

<h5>vmLM1</h5>
The vmLM1 is the Kubernetes host itself. It will act as Control Plane (Master Node) & Data Plane (Worker Node).

**VM Specs:**
- OS: Ubuntu 22.04.3 LTS
- Kernel: Linux
- CPU: 4 vCPUs
- RAM: 12 GB
- Storage: /dev/sda 16 GB
- Network: Access to vmKL1 & Internet
- IP: 192.168.110.60

**Installed Software:**
- minikube version: v1.32.0

The container network is set up in a way that only necessary communication is allowed. UFW is active and configured. Open ports to the Kubernetes Node (vmLM1):

- 22/TCP -> SSH
- 443/TCP -> Kubernetes API
- 30703/TCP -> express-mongodb-app
- 8001/TCP -> Kubernetes Dashboard
- 31185/TCP -> Argocd (optional)

As far as the container network goes, only pods that need external access are configured with NodePort. All other pods are configured with Cluster IP for Kubernetes internal-only communication.

<h5>Manual Setup Guide</h5>
<h5>vmKL1</h5>

```bash
# Update the System
sudo apt update -y
sudo apt upgrade -y
```
```bash
# Port forward , Enable & open necessary ports on the server firewall
sudo ufw allow 22/tcp
sudo ufw allow 443/tcp
sudo ufw allow 80
sudo ufw allow 8001 # for minikube dashboard
sudo ufw allow 30703 # for express-mongodb-app
sudo ufw allow 31185 # for argocd (optional)
sudo ufw enable
```
<h5>Enable SSH Access</h5>
Port 22

```bash
# Restart sshd
sudo systemctl restart sshd
```
```bash
# Update the System
sudo apt update -y
sudo apt upgrade -y
```
```bash
# Install Docker
sudo apt install docker.io
docker --version # Check Docker version
```
```bash
# Download Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version # Check Minikube version
```
```bash
# Install Kubectl
sudo snap install kubectl --classic
kubectl version # Check Kubectl version
```
```bash
# Start Minikube with Docker driver
minikube start --driver=docker
sudo usermod -aG docker $USER && newgrp docker
minikube node add —worker
kubectl label node <node_name> node-role.kubernetes.io/worker=worker
kubectl get nodes
```
```bash
# Update local apt cache
sudo apt update
```
```bash
# Create a namespace for the app
kubectl create namespace nodejs-mongodb-app
```
```bash
# Create a project folder for deployment
mkdir ~/kube
