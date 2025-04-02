CD pipeline for GitOps project:
https://github.com/eranzaksh/GitOps_Project

# Python Web-App CD Pipeline

This repository contains the configuration and code for the Continuous Deployment (CD) pipeline of my **Python Web-App**. It leverages **Jenkins**, **Terraform**, **Helm**, and **ArgoCD** to deploy the application on AWS EKS with high availability and automated version control.

## Key Components

### 1. **Helm Chart**
- **Directory**: `web-app`
- The Helm chart packages the Web-App for deployment.
- **Image Versioning**: The Helm values file includes an `image.tag`, which is dynamically updated in the pipeline with the build number and last commit hash.

### 2. **Terraform Infrastructure**
- **Directory**: `terraform`
- Terraform defines and provisions the AWS resources:
  - **VPC**: Custom Virtual Private Cloud setup using private subnets for better security.
  - **EKS Cluster**: Managed Kubernetes service for running the app.
  - **Ingress Controller**: Creates an AWS Load Balancer for external access.
  - **ArgoCD App**: Deployed in the cluster for GitOps-style deployment.

### 3. **Jenkins Pipeline**
- **File**: `Jenkinsfile`
- This Jenkins pipeline automates the deployment process:
  1. Receives a **build number** and **commit hash** from another CI pipeline (Or as input in manual activation).
  2. Updates the `image.tag` value in the Helm chart.
  3. Pushes changes to the repository, triggering ArgoCD automatic monitor and deploy the new version.

### 4. **Prometheus Monitoring**
- Includes Grafana setup for visualization of the ingress controller connections in the EKS cluster and the web app.

## Workflow
1. **Build Pipeline**:
   - A CI pipeline builds the Docker image for the Web-App.
   - It outputs a **build number** and **commit hash**.
2. **Jenkins Pipeline**:
   - Utilize dynamic agent using AWS.
   - Updates the Helm `image.tag` with the new build information.
   - Pushes the changes to the Helm chart values.
3. **ArgoCD Monitoring**:
   - ArgoCD creates an app for the web-app if not yet exists.
   - ArgoCD detects changes in the Helm chart values.
   - It automatically deploys the updated image to the EKS cluster.
4. **Ingress Controller**:
   - ArgoCD includes an ingress rule with a sub-domain (via [DuckDNS](https://www.duckdns.org/)) to access the ArgoCD, Grafana, and the web-app web UI. access is through HTTPS protocol leveraging cert-manager and Let's encrypt CA. 
   - Exposes the web application via an AWS Load Balancer.
5. **ArgoCD UI**:
   - Accessible using the DuckDNS-hosted domain for deployment visibility.
6. **Grafana Dashboard**:
   - Accessible using the DuckDNS-hosted domain for monitoring visability.

## Accessing the ArgoCD Web UI
- Hostname: Configured using **DuckDNS**.
- Use the provided domain name to monitor deployments and application status.

## AWS Infrastructure Overview
- **VPC**: Networking layer for the EKS cluster.
- **EKS**: Managed Kubernetes environment.
- **Ingress Controller**: Handles external traffic, provisions an AWS Load Balancer.

## Monitoring and Observability
- **Grafana**: Dashboards and metrics for the nginx controller and all traffic flows through it.

## Prerequisites
- **Terraform**: For infrastructure provisioning.
- **Helm**: For packaging the application.
- **Jenkins**: For pipeline execution.
- **DuckDNS**: To access ArgoCD UI, Grafana UI, and the web-app.
- **AWS Account**: For deploying resources.

## How to Use
1. Clone the repository:
   ```bash
   git clone https://github.com/eranzaksh/CD_Project.git
   ```
2. Set up Terraform to provision infrastructure:
   ```bash
   cd terraform
   terraform init
   terraform apply
   ```
3. Configure Jenkins with the provided `Jenkinsfile`.
4. Deploy the application using ArgoCD.
5. Monitor traffic via grafana:
   ```bash
   username: admin
   password: prom-operator
   import dashboard: 14314
   ```

## Repository Structure
```plaintext
terraform/              # Terraform configurations (VPC, EKS, ArgoCD, Ingress Controller, Prometheus)
web-app     /           # Helm chart for the Web-App
Jenkinsfile             # Jenkins pipeline for CD
README.md               # Project documentation
```

## Future Enhancements
- Add secret manager
- Add more observability tools

**Author**: Eran Zaksh  
**Contact**: eranzaksh@gmail.com