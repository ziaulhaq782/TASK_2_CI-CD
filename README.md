# CI/CD Pipeline for Secure Microservices Deployment on AWS

This repository implements a CI/CD pipeline using **GitHub Actions** to build, scan, and deploy a microservices application on AWS using **Docker** and **Terraform**.

## 🚀 Pipeline Features

- ✅ **Source Checkout** via GitHub Actions
- 🔍 **Static Code Analysis** using **SonarQube**
- 🛡️ **Container Vulnerability Scanning** using **Trivy**
- 🏗️ **Multi-stage Docker Builds**
- ☁️ **Infrastructure Provisioning** using **Terraform**
- ✅ **IaC Security Compliance Checks** using **Checkov**

---

## 📁 Folder Structure

.
├── .github/
│ └── workflows/
│ └── ci-cd.yml
├── terraform/
│ ├── main.tf
│ ├── variables.tf
│ └── outputs.tf
├── Dockerfile
├── app/ # Your application source code
├── sonar-project.properties
├── README.md


---

## 🔧 Prerequisites

- AWS account with appropriate IAM permissions
- GitHub repository with GitHub Actions enabled
- Terraform Cloud/Backend configured (or use local state)
- Docker installed locally (for testing)
- SonarQube server or SonarCloud account

---

## 🛠️ GitHub Actions Workflow Overview (`.github/workflows/ci-cd.yml`)

### 🧩 Steps:

1. **Checkout Code**

    ```yaml
    - uses: actions/checkout@v4
    ```

2. **Run Checkov (IaC Compliance)**

    ```yaml
    - name: Checkov - Terraform security scan
      uses: bridgecrewio/checkov-action@master
    ```

3. **Static Code Analysis (SonarQube)**

    ```yaml
    - name: SonarQube Scan
      uses: SonarSource/sonarqube-scan-action@master
    ```

4. **Vulnerability Scan (Trivy)**

    ```yaml
    - name: Trivy scan
      uses: aquasecurity/trivy-action@master
    ```

5. **Multi-Stage Docker Build**

    ```yaml
    - name: Build Docker Image
      run: docker build -t my-app:latest .
    ```

6. **Terraform Init & Apply**

    ```yaml
    - name: Terraform Init & Apply
      run: |
        cd terraform
        terraform init
        terraform apply -auto-approve
    ```

---

## ✅ Environment Secrets Required

Add these in GitHub repository **Settings → Secrets**:

- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `SONAR_TOKEN` (if using SonarCloud)
- `TF_VAR_*` (for Terraform variables, as needed)

---

## 🐳 Multi-stage Dockerfile Example

```Dockerfile
# Stage 1: Build
FROM node:18-alpine as builder
WORKDIR /app
COPY ./app .
RUN npm install && npm run build

# Stage 2: Runtime
FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
CMD ["node", "dist/index.js"]

#Local Testing

docker build -t my-app .
docker run -p 3000:3000 my-app
# Test terraform

cd terraform
terraform init
terraform plan
