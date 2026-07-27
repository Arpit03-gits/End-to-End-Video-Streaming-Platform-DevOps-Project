<div align="center">

# 🎬 End-to-End Video Streaming Platform — DevOps Project

### Production-grade CI/CD pipeline deploying a video streaming app to Kubernetes on AWS

[![AWS](https://img.shields.io/badge/AWS-EC2%20%7C%20VPC%20%7C%20IAM-FF9900?logo=amazon-aws&logoColor=white)](.)
[![Terraform](https://img.shields.io/badge/Terraform-IaC-7B42BC?logo=terraform&logoColor=white)](.)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?logo=jenkins&logoColor=white)](.)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker&logoColor=white)](.)
[![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-326CE5?logo=kubernetes&logoColor=white)](.)
[![SonarQube](https://img.shields.io/badge/SonarQube-Code%20Quality-4E9BCD?logo=sonarqube&logoColor=white)](.)
[![Trivy](https://img.shields.io/badge/Trivy-Vuln%20Scanning-1904DA)](.)
[![OWASP](https://img.shields.io/badge/OWASP-Dependency%20Check-000000?logo=owasp&logoColor=white)](.)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](./LICENSE)

</div>

---

## 📌 Overview

This project takes a **React + TypeScript video streaming application** (a Netflix-style UI) from source code to a **fully automated, secure, cloud-native deployment**. It's built to demonstrate a real DevOps workflow end-to-end — not just "deploy an app," but **provision the infrastructure, gate the code on quality and security, containerize it, scan it, and ship it to Kubernetes** — all triggered by a single `git push`.

The goal of this repo is to show the complete lifecycle a DevOps/Cloud engineer owns in production: **Infrastructure as Code → CI/CD → Security Scanning → Containerization → Orchestration → Alerting.**

## 🏗️ Architecture

![Architecture Diagram](./assets/arch-diag.gif)

**Flow:** Developer pushes code → Jenkins pulls from GitHub → dependencies installed → SonarQube static analysis + Quality Gate → OWASP Dependency Check → Trivy filesystem scan → Docker image built & pushed to Docker Hub → Trivy image scan → deployed to Kubernetes → Slack notification on build result.

## ⚙️ Tech Stack

| Layer                     | Tools                                                              |
|---------------------------|---------------------------------------------------------------------|
| **Infrastructure as Code**| Terraform (custom VPC, subnets, IAM roles, EC2 fleet)               |
| **CI/CD**                 | Jenkins (declarative pipeline)                                      |
| **Code Quality**          | SonarQube + Quality Gate enforcement                                 |
| **Security**               | OWASP Dependency-Check, Trivy (filesystem + image scanning)         |
| **Containerization**       | Docker, Docker Hub registry                                          |
| **Orchestration**         | Kubernetes (Deployment + NodePort Service)                           |
| **Notifications**         | Slack integration for build status                                   |
| **Application**           | React 18, TypeScript, Vite, Redux Toolkit, MUI                       |
| **Cloud Provider**        | AWS (EC2, VPC, IAM, SSM)                                              |

## 🚀 What This Pipeline Actually Does

1. **Provisions infrastructure with Terraform** — a custom VPC with 4 public subnets across 4 AZs, an internet gateway, route tables, a security group, and an IAM role/instance profile with SSM access, standing up 4 EC2 instances (Jenkins server, monitoring server, Kubernetes master, Kubernetes worker).
2. **Checks out and builds the app** — installs dependencies for the React/TypeScript frontend.
3. **Runs static code analysis** — SonarQube scans the `src` directory and enforces a Quality Gate before the pipeline is allowed to proceed.
4. **Scans for vulnerabilities twice** — OWASP Dependency-Check on third-party libraries, then Trivy on the filesystem *and* the built Docker image.
5. **Builds & ships the container** — builds a Docker image with build-time secrets injected via Jenkins credentials, tags it, and pushes it to Docker Hub.
6. **Deploys to Kubernetes** — applies a Deployment (2 replicas) and a NodePort Service via `kubectl`, using a Jenkins-managed kubeconfig credential.
7. **Reports status to Slack** — every build (success, failure, or unstable) posts a formatted notification to a dedicated channel.

## 📂 Project Structure

```
.
├── Application-Code/     # React + TypeScript video streaming app (source, Dockerfile)
├── Terraform/            # IaC: VPC, subnets, IAM, EC2 fleet provisioning
├── Jenkins/              # Jenkinsfile — the full CI/CD pipeline definition
├── Kubernetes/           # Deployment & Service manifests
├── assets/               # Architecture diagram
└── README.md
```

## 🧰 Getting Started

**Prerequisites:** AWS account, Terraform, a Jenkins server with the `nodejs`, `sonar-server`, `owasp-dp-check`, and `docker` tools/plugins configured, `kubectl` access to a cluster, and a Docker Hub account.

```bash
# 1. Provision infrastructure
cd Terraform
terraform init
terraform plan
terraform apply

# 2. Build and run the app locally (optional sanity check)
cd ../Application-Code
docker build --build-arg TMDB_V3_API_KEY=your_api_key_here -t streaming-app .
docker run --rm -p 80:80 streaming-app

# 3. Trigger the Jenkins pipeline
# Point a Jenkins job at this repo's Jenkinsfile — it handles the rest.

# 4. Verify the Kubernetes deployment
kubectl apply -f Kubernetes/deployment.yml
kubectl apply -f Kubernetes/service.yml
kubectl get svc
```

## 🔐 Security & Quality Gates

This pipeline treats security scanning as a **blocking step**, not an afterthought:
- **SonarQube Quality Gate** — code must meet quality standards before the build continues.
- **OWASP Dependency-Check** — flags known CVEs in third-party dependencies.
- **Trivy (filesystem + image)** — scans the source tree and the final container image for vulnerabilities before deployment.

## 📈 Roadmap / Next Improvements

- [ ] Add Prometheus + Grafana on the monitoring EC2 instance for cluster/app observability
- [ ] Migrate `kubectl apply` steps to GitOps (ArgoCD) for declarative, drift-free deployments
- [ ] Move from NodePort to an Ingress Controller / LoadBalancer for production traffic
- [ ] Add Ansible for configuration management of the provisioned EC2 fleet

## 👤 Author

**Arpit Kumar Mishra**
Pre-Final year B.Tech IT student | Cloud & DevOps Enthusiast

- GitHub: [Arpit03-gits](https://github.com/Arpit03-gits)
- LinkedIn: [arpit-mishra-341203305](https://www.linkedin.com/in/arpit-mishra-341203305)

## 📄 License

This project is licensed under the [MIT License](./LICENSE).
