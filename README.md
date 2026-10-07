# 🚀 Cloud-Native Microservices E-Commerce Platform

This project demonstrates the complete **DevOps lifecycle** for the **OpenTelemetry Demo**, a realistic microservices-based e-commerce application. The project focuses on implementing an **end-to-end DevOps workflow** covering **containerization, CI/CD automation, Infrastructure as Code, Kubernetes deployment, DevSecOps, and monitoring/observability** using AWS and industry-standard tools, following the flow: **Source Code → CI/CD → Security → Docker → Kubernetes → AWS → Monitoring & Observability**.


# 🛒 Application Used

For this project, we are using the **OpenTelemetry Astronomy Shop Demo**, a microservices-based e-commerce application provided by the OpenTelemetry project.

The application contains multiple microservices that communicate with each other, making it a good foundation for implementing and demonstrating an end-to-end DevOps workflow. ([GitHub][1])

**OpenTelemetry Demo Repository:**
[View the OpenTelemetry Demo on GitHub](https://github.com/open-telemetry/opentelemetry-demo?utm_source=chatgpt.com)

**Microservices Architecture:**
[View the OpenTelemetry Demo Architecture](https://opentelemetry.io/docs/demo/architecture/?utm_source=chatgpt.com)

```text
OpenTelemetry Demo Application
            ↓
Docker
            ↓
CI/CD with Jenkins
            ↓
Security & DevSecOps
            ↓
Kubernetes
            ↓
AWS Infrastructure
            ↓
Monitoring & Observability
```

This makes it clear that **the OpenTelemetry Demo is the application we are DevOps-ifying**, rather than claiming that we built the e-commerce application ourselves. 

[1]: https://github.com/open-telemetry/opentelemetry-demo?utm_source=chatgpt.com "GitHub - open-telemetry/opentelemetry-demo: This repository contains the OpenTelemetry Astronomy Shop, a microservice-based distributed system intended to illustrate the implementation of OpenTelemetry in a near real-world environment. · GitHub"

# 🚀  Quick Start

# Section 1: Installation & Prerequisites

Before starting the DevOps implementation, we will prepare an AWS EC2 environment and install the required tools.

## Step 1: Create an AWS EC2 Instance

We will use an **EC2 instance** as our primary DevOps environment where we will install and configure the required tools.

**Recommended configuration:**

* **AMI:** Ubuntu Server
* **Instance Type:** `t2.large`
* **vCPUs:** 2
* **Memory:** 8 GB
* **Storage:** 20–30 GB or more
* **Architecture:** 64-bit (x86)
* **Security Group:** Allow SSH (`22`) from your IP
* Create/download the required **Key Pair** for SSH access.

After launching the instance, connect to it using SSH:

```bash
ssh -i your-key.pem ubuntu@<EC2-PUBLIC-IP>
```

---

## Step 2: Install Docker

We will install Docker using Docker's official APT repository.

### 2.1 Set up Docker's APT repository

```bash
sudo apt update
sudo apt install ca-certificates curl
```

Add Docker's official GPG key:

```bash
sudo install -m 0755 -d /etc/apt/keyrings

sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
-o /etc/apt/keyrings/docker.asc

sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Add Docker's official repository:

```bash
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Architectures: $(dpkg --print-architecture)
Signed-By: /etc/apt/keyrings/docker.asc
EOF
```

Update the package index:

```bash
sudo apt update
```

### 2.2 Install Docker Engine

Install the latest Docker packages:

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 2.3 Verify Docker Installation

Check the Docker version:

```bash
docker --version
```

Check whether Docker is running:

```bash
sudo systemctl status docker
```
<img width="1225" height="171" alt="image" src="https://github.com/user-attachments/assets/8624ac13-c307-4d86-bf71-e1d1a698fdbd" />


### 2.4 Docker Permissions (Optional)

If you don't want to use `sudo` with Docker commands, add the current Ubuntu user to the `docker` group:

<img width="1127" height="132" alt="image" src="https://github.com/user-attachments/assets/72390503-c7e7-4015-bacf-4f5dc79c8b5b" />

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Now verify that Docker works without `sudo`:

```bash
docker ps
```
<img width="950" height="122" alt="image" src="https://github.com/user-attachments/assets/6de8362f-e138-437a-9d07-d621390cc3f0" />


> **Note:** We'll use this official Docker installation method instead of `sudo apt install docker.io`, because this project README should follow Docker's official repository and package installation process.


## Step 3: Install kubectl

We will install `kubectl` using the official Kubernetes installation method.

### 3.1 Download the latest stable kubectl binary

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

### 3.2 Download the checksum file

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
```

### 3.3 Validate the downloaded binary

```bash
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
```

Expected output:

```text
kubectl: OK
```

This confirms that the downloaded `kubectl` binary matches the official checksum.

### 3.4 Install kubectl

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### 3.5 Verify the installation

```bash
kubectl version --client
```

<img width="1211" height="357" alt="image" src="https://github.com/user-attachments/assets/6ebe773a-8a68-4808-ae9d-0f2e6aedbd51" />

### 📌 Note

> **Always check the official documentation for the latest installation commands and instructions**, as commands and installation methods may change with newer versions.
, refer to the [official Kubernetes documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/).


---

## Step 4: Install Terraform

Add the HashiCorp GPG key:

```bash
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

Add the HashiCorp repository:

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Update packages and install Terraform:

```bash
sudo apt update && sudo apt install terraform -y
```

Verify:

```bash
terraform --version
```

<img width="620" height="68" alt="image" src="https://github.com/user-attachments/assets/de35c07d-12c5-4f70-973f-5c8f13664534" />


After these prerequisites are ready, we can move to the **next section: AWS Infrastructure Setup using Terraform**.


# 🐳 Section 2: Run the Application Locally Without Kubernetes

## Step 1: Clone the Repository

```bash
git clone https://github.com/open-telemetry/opentelemetry-demo.git
```

## Step 2: Go to the Repository

```bash
cd opentelemetry-demo
```

## Step 3: Check Docker Compose

```bash
docker compose version
```

## Step 4: Run the Application

```bash
docker compose up -d
```
<img width="1186" height="618" alt="image" src="https://github.com/user-attachments/assets/63c0b8f2-161c-4764-bba5-29ce6f7912ca" />

Docker Compose runs the multiple microservices of the OpenTelemetry Demo together.

**Access the application:**

```text
http://EC2-Public-IP:8080/
```
> **Note:** Make sure port `8080` is allowed in the EC2 Security Group.

<img width="1893" height="612" alt="image" src="https://github.com/user-attachments/assets/2fd79660-7bf3-479c-bfd2-44ee1694ad29" />

# 🔹 Section 3: Understanding Microservices

In this section, we will **perform practical work on different microservices** of the OpenTelemetry Demo, such as **Product Catalog, Cart, and Recommendation**. These microservices are written in **different programming languages**, giving us hands-on experience working with a multi-language microservices application.













