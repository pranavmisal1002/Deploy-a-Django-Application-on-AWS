# 🚀 Deploy a Django Application on AWS using ECS & ECR (Production Ready)

This comprehensive guide demonstrates how to deploy a **Django production application** on AWS using:

- Docker for containerization  
- Amazon ECR for secure container image storage  
- Amazon ECS (Fargate) for container orchestration  

It covers the complete **DevOps deployment pipeline** — from building Docker images to running scalable production containers with security and monitoring best practices.

## 🧰 Technologies Used

- Python 3.9+  
- Django  
- Docker  
- AWS EC2  
- Amazon ECR  
- Amazon ECS (Fargate)

## 📚 Prerequisites

### 🔧 Technical Requirements

- Python 3.9+  
- Docker Engine / Docker Desktop  
- AWS Account  
- AWS CLI configured (`aws configure`)  

### 📖 Basic Knowledge Of

- Django  
- Docker  
- AWS services

## 📌 Project Features

- 🐳 Containerized Django application using Docker  
- 📦 Secure image storage with Amazon ECR  
- ☁️ Serverless container deployment using AWS ECS Fargate

## 📌 Architecture Overview
Django App → Docker Image → Amazon ECR → ECS Cluster → Task Definition → Service → Public IP → Live Django Application

## 🛠️ Complete Deployment Steps
### ✅ Step 1: Launch an EC2 Instance (For Building Docker Image)

1. Launch an **Ubuntu EC2 instance**
2. Configure **Security Group inbound rules**:
   - Allow **SSH (22)**
   - Allow **Custom TCP port 8000** (for Django testing)

Connect to the EC2 instance:

```bash
ssh -i your-key.pem ubuntu@<EC2_PUBLIC_IP>
```
Update the system:
```bash
sudo apt update -y
sudo apt upgrade -y
```
### ✅ Step 2: Install Docker

Install Docker on the EC2 instance:

```bash
sudo apt install docker.io -y
```
Start and enable Docker service:
```bash
sudo systemctl start docker
sudo systemctl enable docker
```
Verify Docker installation:
```bash
docker --version
```
### ✅ Step 3: Clone Django Project

Clone your Django project repository:

```bash
git clone https://github.com/pranavmisal1002/Deploy-a-Django-Application-on-AWS.git
```
Navigate into the project directory:
```bash
cd myproject
```
### ✅ Step 4: Build Docker Image

Build the Docker image for the Django application:

```bash
sudo docker build -t django-app .
```
Verify the Docker image:
```bash
sudo docker images
```
---
## ☁️ AWS ECR Setup
### ✅ Step 5: Create ECR Repository

1. Open **AWS Console** → **Amazon ECR**
2. Click **Create repository**
3. Copy the repository URI (example):

```text
123456789012.dkr.ecr.ap-south-1.amazonaws.com/django-app
```
> ✅ **Note:** In AWS ECR, click on your repository name to open the private repository.  
> Then select **View push commands** on the right-hand side — you can directly copy and paste the provided commands to authenticate Docker and push your image to ECR.


### ✅ Step 6: Authenticate Docker to Amazon ECR

Login Docker to your ECR registry:

```bash
aws ecr get-login-password --region ap-south-1 | \
docker login --username AWS --password-stdin <ECR_URI>
```
### ✅ Step 7: Tag Docker Image for ECR

Tag the Docker image with your ECR repository URI:

```bash
docker tag django-app:latest <ECR_URI>:latest
```
Example:
```bash
docker tag django-app:latest 123456789012.dkr.ecr.ap-south-1.amazonaws.com/django-app:latest
```
### ✅ Step 8: Push Image to Amazon ECR

Push the Docker image to your ECR repository:

```bash
docker push <ECR_URI>:latest
```
---
## 🚀 ECS Deployment Using Fargate
### ✅ Step 9: Create ECS Cluster

1. Open **AWS Console** → **Amazon ECS**
2. Go to **Clusters** → **Create Cluster**
3. Choose:
   - 👉 **Fargate (Serverless)**
### ✅ Step 10: Create Task Definition

Create a new ECS Task Definition with the following configuration:

- **Launch type:** Fargate  
- **Container image:** Amazon ECR Image URI  
- **Port mapping:**  
  - `8000 → 8000`  
- **CPU & Memory:** Default values (sufficient for testing)

### ✅ Step 11: Create ECS Service

Create an ECS Service using the following settings:

- Choose the created **ECS Cluster**
- Select the **Task Definition**
- **Number of tasks:** `1`
- Enable **Public IP access**

Click **Create Service** 🚀
### ⏳ Step 12: Wait for Container to Start

ECS will pull the image from Amazon ECR and start the container automatically.

Navigate to:

**ECS → Cluster → Tasks → Running Task**

Copy the assigned **Public IP address** once the task is running.

## 🌍 Access Django Application

Open your browser and visit:

```text
http://<PUBLIC_IP>:8000/hello/
http://<PUBLIC_IP>:8000/admin/
```
