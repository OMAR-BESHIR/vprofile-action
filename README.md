# VProfile Application CI/CD Pipeline 

This repository contains the automated CI/CD pipeline for deploying the **VProfile Java Application** onto an Amazon EKS cluster. This workflow ensures that every code change is tested, scanned for quality, and deployed seamlessly.

## 🏗️ Deployment Architecture
The pipeline follows a modern DevSecOps approach, integrating security and quality checks before the deployment phase.

![System Architecture](images/Screenshot%202026-03-15%20163518.png)

---

## 🛠️ CI/CD Workflow Breakdown (`vprofile actions`)

The pipeline is triggered via `workflow_dispatch` and consists of three main jobs:

### 1. 🔍 Testing & Code Quality
In this stage, we ensure the code meets production standards:
* **Unit Tests:** Running `mvn test` to verify logic.
* **Checkstyle:** Ensuring code formatting adheres to standards.
* **SonarCloud Scan:** Deep static analysis to detect bugs, vulnerabilities, and code smells.
* **Quality Gate:** A blocking check that prevents deployment if the code quality is below the threshold.

### 2. 📦 Build & Publish to ECR
Once the tests pass, the application is containerized:
* **Docker Build:** Creating a lightweight image using the project's `Dockerfile`.
* **Amazon ECR:** The image is pushed to the AWS Elastic Container Registry with both a `latest` tag and a unique `build_number`.

![Amazon ECR Repository](images/Screenshot%202026-03-12%20164224.png)

### 3. 🚢 Deployment to EKS (Helm)
The final stage handles the orchestration:
* **AWS Auth:** Configuring credentials and `kubeconfig`.
* **Docker Login:** Creating a Kubernetes secret (`regcred`) to allow EKS to pull from private ECR.
* **Helm Deploy:** Using Helm Charts to deploy or upgrade the application stack (`vprofile-stack`) with the new image tag.

![GitHub Actions Success](images/Screenshot%202026-03-15%20162735.png)

---

## 🌐 Final Result
After a successful pipeline run, the application is live and accessible through the Ingress Controller's DNS/LoadBalancer.

![Final Application Access](images/Screenshot%202026-03-15%20163602.png)

---

## ⚙️ Configuration Requirements

To run this pipeline, the following **GitHub Secrets** must be configured:

| Secret Name | Description |
| :--- | :--- |
| `AWS_ACCESS_KEY_ID` | AWS IAM User Access Key |
| `AWS_SECRET_ACCESS_KEY` | AWS IAM User Secret Key |
| `REGISTRY` | AWS ECR Registry URL |
| `SONAR_TOKEN` | Authentication token for SonarCloud |
| `SONAR_ORGANIZATION` | Your SonarCloud Organization key |
| `SONAR_PROJECT_KEY` | Your SonarCloud Project key |
| `SONAR_HOST_URL` | https://sonarcloud.io |
