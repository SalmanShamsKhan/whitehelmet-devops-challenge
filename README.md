Terraform Infrastructure Deployment - WhiteHelmet IAC CI
Overview
This repository contains the Infrastructure as Code (IAC) setup for deploying an AWS EKS cluster using Terraform. The deployment process is automated using GitHub Actions, and the workflow file terraform.yaml handles the provisioning of AWS resources.

Real-World Implementation vs. Assignment Implementation
Aspect	Real-World Best Practice	Assignment Implementation
Repository Structure	Separate iac-repo for infrastructure and app-repo for applications.	Single repository with infrastructure and application.
Triggering Mechanism	Auto-trigger terraform plan on stage branch and terraform apply on merge to main.	Manual trigger (workflow_dispatch).
State Management	Remote backend with S3 & DynamoDB for state locking.	S3 backend but no state locking in DynamoDB.
Approval Process	Terraform Plan requires a manual approval before apply.	Terraform Apply runs immediately after plan in manual execution.
Workflow Execution - terraform.yaml
This workflow is responsible for:

Provisioning AWS networking (VPC, Subnets, NAT Gateway, Route Tables).
Deploying an EKS cluster along with required IAM roles.
Setting up an ALB ingress controller for traffic routing.
Terraform Workflow Steps
1️⃣ Checkout the Source Code
yaml
Copy
Edit
- name: Checkout Source Code
  uses: actions/checkout@v4
Retrieves the latest Terraform code from the repository.
2️⃣ Setup Terraform
yaml
Copy
Edit
- name: Setup Terraform
  uses: hashicorp/setup-terraform@v2
Installs Terraform on the runner.
3️⃣ Initialize Terraform
yaml
Copy
Edit
- name: Terraform Init
  id: init
  run: terraform init -backend-config="bucket=$BUCKET_TF_STATE"
Initializes Terraform and connects to an S3 backend to store the Terraform state.
4️⃣ Validate Terraform Code
yaml
Copy
Edit
- name: Terraform Format
  id: fmt
  run: terraform fmt -check

- name: Terraform Validate
  id: validate
  run: terraform validate
Ensures that the Terraform syntax is correctly formatted and valid.
5️⃣ Generate Execution Plan
yaml
Copy
Edit
- name: Terraform Plan
  id: plan
  run: terraform plan -no-color -input=false -out planfile
  continue-on-error: true
Creates a Terraform execution plan, which previews the changes before applying them.
6️⃣ Conditional Approval in Real-World (Manual Dispatch in Assignment)
Real-World Scenario	Assignment Implementation
Terraform plan is reviewed and manually approved before merging stage → main.	Since this is manual dispatch, terraform apply is triggered directly after plan.
yaml
Copy
Edit
- name: Terraform Plan Status
  if: steps.plan.outcome == 'failure'
  run: exit 1
Stops execution if terraform plan fails.
7️⃣ Apply Terraform Configuration
yaml
Copy
Edit
- name: Terraform Apply
  id: apply
  if: github.event_name == 'workflow_dispatch'
  run: terraform apply -auto-approve -input=false -parallelism=1 planfile
Applies Terraform changes and provisions the AWS infrastructure.
8️⃣ Configure AWS Credentials
yaml
Copy
Edit
- name: Configure AWS credentials
  uses: aws-actions/configure-aws-credentials@v1
  with:
    aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
    aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    aws-region: ${{ env.AWS_REGION }}
Grants GitHub Actions access to AWS.
9️⃣ Fetch Kubernetes Config for EKS
yaml
Copy
Edit
- name: Get Kube config file
  id: getconfig
  if: steps.apply.outcome == 'success'
  run: aws eks update-kubeconfig --region ${{ env.AWS_REGION }} --name ${{ env.EKS_CLUSTER }}
Updates the kubectl configuration to interact with the newly created EKS cluster.
🔟 Deploy AWS Load Balancer Controller
yaml
Copy
Edit
- name: Install Ingress Controller
  if: steps.apply.outcome == 'success' && steps.getconfig.outcome == 'success'
  run: kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.1.3/deploy/static/provider/aws/deploy.yaml
Deploys an Nginx ingress controller which creates an Application Load Balancer (ALB) in AWS.
Expected Outcome
When this workflow is executed:

Terraform provisions AWS networking components (VPC, subnets, NAT Gateway).
An EKS cluster is created along with required IAM roles.
ALB Ingress Controller is deployed, making it possible to route external traffic.
Next Steps: Deploying Applications
After this Terraform workflow completes, the ELK Stack Deployment workflow (elk-deployment.yaml) can be triggered to:

Deploy Elasticsearch, Kibana, and Logstash into the EKS cluster.
Configure Helm charts for monitoring and logging.
Conclusion
This terraform.yaml workflow enables repeatable, automated AWS infrastructure deployment. In a real-world setup:

This workflow would reside in a separate IAC repository.
Terraform apply would be restricted until an engineer manually approves the terraform plan changes.
The AWS backend should use DynamoDB for state locking.
🚀 This implementation ensures a scalable, automated AWS infrastructure setup using Terraform & GitHub Actions! 🚀
