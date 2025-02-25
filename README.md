# GitOps CI/CD Pipeline with GitHub Actions, Terraform, AWS EKS, Docker, and SonarCloud

## **Project Overview**
This GitOps-based CI/CD pipeline automates infrastructure provisioning and application deployment using **GitHub Actions, Terraform, AWS EKS, Docker, Helm, Maven, and SonarCloud**. The pipeline ensures **continuous integration, security scanning, and automated deployment** of containerized applications into a highly available Kubernetes cluster (EKS). 

It integrates **Terraform** for infrastructure management, **SonarCloud** for code quality analysis, and **Helm** for Kubernetes deployment.

## **Architecture Diagram**
![GitOps CI/CD Pipeline](terraform%20and%20app%20code%20workflow%20devops%20gitops.jpeg)

---

## **Flow of Execution**
### **1. Code Development and Version Control**
- Developers use **Visual Studio Code (VS Code)** for coding.
- Code is pushed to **GitHub**, which acts as the **central version control system**.
- **GitHub Actions** are triggered for two separate workflows:
  - **Terraform Workflow**: Infrastructure provisioning.
  - **Build, Test, and Deploy Workflow**: Application build, security scanning, and deployment.

### **2. Infrastructure Provisioning with Terraform**
- Developers work on a feature branch and push **Terraform** infrastructure changes.
- **GitHub Actions** fetch the latest **stage branch**.
- **Terraform Plan** is executed to validate infrastructure changes.
- A **Pull Request (PR)** is created for review.
- Upon approval, the **PR is merged** into the main branch.
- **Terraform Apply** executes to provision AWS resources, including:
  - **Amazon EKS (Elastic Kubernetes Service) Cluster**
  - **Amazon ECR (Elastic Container Registry) for storing container images**
  - **VPC subnet for secure networking**

### **3. Build, Test & Deploy Workflow**
- **GitHub Actions** trigger the **CI pipeline** when new code is pushed.
- The pipeline performs the following steps:
  1. **Fetch Source Code** - Retrieves the latest application code.
  2. **Build with Maven** - Compiles the code and generates an application artifact.
  3. **Static Code Analysis with SonarCloud** - Ensures code quality and security compliance.
  4. **Containerization with Docker** - Builds the application image and pushes it to **Amazon ECR**.
  5. **Deployment to Kubernetes with Helm** - Deploys the containerized application to **AWS EKS**.
  6. **Monitoring & Logging** - Ensures application health and performance.

---

## **Technology Stack**
| Component | Technology Used |
|-----------|----------------|
| **CI/CD Tool** | GitHub Actions |
| **Infrastructure as Code (IaC)** | Terraform |
| **Cloud Provider** | AWS (EKS, ECR, VPC) |
| **Containerization** | Docker |
| **Orchestration** | Kubernetes (Helm Charts) |
| **Build System** | Maven |
| **Security & Code Quality** | SonarCloud |
| **Source Code Management** | GitHub |

---

## **Benefits of the Project**
✅ **Automated Infrastructure Provisioning** - Terraform ensures that AWS infrastructure is dynamically created and managed.  
✅ **Improved Code Quality** - SonarCloud integration ensures security and compliance.  
✅ **Scalable Kubernetes Deployment** - AWS EKS provides high availability and auto-scaling for containerized applications.  
✅ **Seamless CI/CD with GitHub Actions** - Automates builds, testing, security scans, and deployments.  
✅ **GitOps-Driven Workflow** - Ensures that infrastructure and application deployments are version-controlled.  

---

## **Conclusion**
This **GitOps-based CI/CD pipeline** provides a **fully automated workflow** for **provisioning infrastructure** and **deploying containerized applications** to **AWS EKS** using **Terraform, Docker, and Helm**. 

By integrating **GitHub Actions, SonarCloud, and Amazon ECR**, the solution ensures **security, scalability, and efficiency** in software delivery. 🚀
