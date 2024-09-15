# E-Commerce Microservices Deployment with AWS EKS & CI/CD 🚀

## Overview

This project demonstrates how to deploy an **E-commerce microservices application** on **AWS Elastic Kubernetes Service (EKS)** using Docker containers. Additionally, we will automate the deployment using a **CI/CD pipeline** (Jenkins/GitLab CI) and integrate **Amazon RDS** for database management. Monitoring is implemented using **Prometheus and Grafana**, and the infrastructure is provisioned using **Terraform**.

## Project Architecture

![Architecture Diagram](https://example.com/your-architecture-image)

- **Cloud Infrastructure:** AWS (EKS, EC2, RDS, VPC, S3, IAM, Route 53)
- **Microservices:** User Service, Product Service, Order Service
- **Containerization:** Docker
- **Orchestration:** Kubernetes on AWS EKS
- **CI/CD:** Jenkins or GitLab CI (with Docker, Kubernetes, Helm)
- **Database:** Amazon RDS (MySQL/PostgreSQL)
- **Monitoring:** Prometheus & Grafana

---

## Prerequisites

- AWS Account
- Docker installed locally
- kubectl installed and configured with AWS EKS
- AWS CLI installed and configured
- Jenkins/GitLab CI for CI/CD automation
- Terraform (for Infrastructure as Code)
- Python (Flask/Django) or Java (Spring Boot) knowledge for microservices

---

## Project Steps

### 1. Setting Up AWS Infrastructure

#### 1.1 Create a VPC
- Navigate to the **AWS Management Console** → **VPC**.
- Create a VPC with the following settings:
  - **VPC Name:** `ecommerce-vpc`
  - **IPv4 CIDR block:** `10.0.0.0/16`

#### 1.2 Create Public and Private Subnets
- Create **2 public subnets** (`10.0.1.0/24`, `10.0.2.0/24`) and **2 private subnets** (`10.0.3.0/24`, `10.0.4.0/24`).

#### 1.3 Create an Internet Gateway
- Go to **Internet Gateway** → **Create Internet Gateway**.
- Attach the Internet Gateway to your `ecommerce-vpc`.

#### 1.4 Configure Route Tables
- Create a **Route Table** for public subnets and associate it with the Internet Gateway for internet access (`0.0.0.0/0`).

---

### 2. Containerizing the Application

Each microservice (User, Product, Order) is containerized using **Docker**.

#### 2.1 Dockerfile Example (User Service)

```Dockerfile
# Base image
FROM python:3.9-slim

# Set the working directory
WORKDIR /app

# Copy the current directory contents into the container
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Expose the port the app runs on
EXPOSE 5000

# Run the application
CMD ["python", "app.py"]


**# 2.2 Build and Test Docker Image Locally**
docker build -t user-service:latest .
docker run -p 5000:5000 user-service

# Repeat this step for the Product and Order services.

**# 3. Setting Up AWS EKS Cluster**
3.1 Create an EKS Cluster
Go to AWS Management Console → EKS → Create Cluster.
Select the VPC and subnets created earlier.
Configure worker nodes using EC2 instances (e.g., t3.medium).
3.2 Install and Configure kubectl
Use the AWS CLI to set up your EKS environment.

aws eks --region <region> update-kubeconfig --name ecommerce-cluster


**4. Deploying Microservices to EKS**
4.1 Kubernetes Manifests
Each microservice is deployed using Kubernetes Deployment and Service YAML files.

user-service-deployment.yaml:
apiVersion: apps/v1
kind: Deployment
metadata:
  name: user-service
spec:
  replicas: 3
  selector:
    matchLabels:
      app: user-service
  template:
    metadata:
      labels:
        app: user-service
    spec:
      containers:
      - name: user-service
        image: user-service:latest
        ports:
        - containerPort: 5000


# user-service-service.yaml:
apiVersion: v1
kind: Service
metadata:
  name: user-service
spec:
  type: LoadBalancer
  selector:
    app: user-service
  ports:
  - protocol: TCP
    port: 80
    targetPort: 5000

# Deploy the microservices:
kubectl apply -f user-service-deployment.yaml
kubectl apply -f user-service-service.yaml

**5. Setting up CI/CD Pipeline**
5.1 Jenkinsfile Example for CI/CD
This Jenkinsfile automates Docker image building, pushing to Amazon ECR, and deploying to EKS.
pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t user-service .'
      }
    }
    stage('Push to ECR') {
      steps {
        sh 'docker tag user-service:latest <your-aws-account-id>.dkr.ecr.<region>.amazonaws.com/user-service:latest'
        sh 'docker push <your-aws-account-id>.dkr.ecr.<region>.amazonaws.com/user-service:latest'
      }
    }
    stage('Deploy to EKS') {
      steps {
        sh 'kubectl apply -f k8s/user-service-deployment.yaml'
      }
    }
  }
}

**## 6. Setting Up Amazon RDS Database**
6.1 Create an RDS Database
Navigate to Amazon RDS → Create Database.
Select MySQL or PostgreSQL.
Set up VPC and security groups to allow your EKS cluster to access the RDS instance.
6.2 Update Microservices to Use RDS
Update your microservices to connect to the RDS endpoint using environment variables.

# **7. Monitoring with Prometheus & Grafana**
7.1 Deploy Prometheus & Grafana on EKS
Install Prometheus and Grafana using Helm charts:
bash
Copy code
helm install prometheus stable/prometheus
helm install grafana stable/grafana
7.2 Configure Dashboards
Access Grafana, set up Prometheus as a data source, and create dashboards for monitoring your services.

# **Conclusion**
By following these steps, you have successfully deployed an E-commerce microservices application on AWS EKS with CI/CD automation and integrated Amazon RDS for persistent storage. The application is scalable, resilient, and monitored using Prometheus and Grafana.

Feel free to fork this repository and enhance the project with more features!


### **Instructions:**
- Replace the **architecture diagram** placeholder with your actual image URL (hosted or GitHub image link).
- Adjust paths in the `Jenkinsfile`, Kubernetes YAML files, and service-specific details as per your project.

This README will give clear guidance and help others replicate your project.





