# 🚀 Django Web Application – DevSecOps CI/CD Pipeline

This repository demonstrates a **complete DevSecOps CI/CD pipeline** for a Django web application using **Jenkins**, **Docker**, and multiple **security scanning tools** such as **Gitleaks**, **Trivy**, and **SonarQube**.

The pipeline automates **code checkout, security scans, code quality analysis, containerization, image scanning, deployment**, and **email notifications**.

---

## 🛠️ Tech Stack & Tools

* **Application**: Django (Python)
* **CI/CD**: Jenkins (Declarative Pipeline)
* **Containerization**: Docker & Docker Compose
* **Code Quality**: SonarQube
* **Secrets Scanning**: Gitleaks
* **Vulnerability Scanning**: Trivy (Filesystem & Docker Image)
* **Container Registry**: Docker Hub
* **Notifications**: Jenkins Email Extension Plugin

---

## 📌 Pipeline Overview

The Jenkins pipeline performs the following stages:

1. **Git Clone**
2. **Gitleaks Scan** – Detect hardcoded secrets
3. **Trivy Filesystem Scan** – Scan source code dependencies
4. **SonarQube Scan** – Code quality & static analysis
5. **Quality Gate Check**
6. **Docker Image Build**
7. **Trivy Image Scan** – Scan container image vulnerabilities
8. **Push Image to Docker Hub**
9. **Deploy Application using Docker Compose**
10. **Email Notification with Security Reports**

---

## 🔄 Jenkins Pipeline Flow

```text
Git Clone
   ↓
Gitleaks Scan
   ↓
Trivy FS Scan
   ↓
SonarQube Scan
   ↓
Quality Gate
   ↓
Docker Build
   ↓
Trivy Image Scan
   ↓
Docker Hub Push
   ↓
Docker Compose Deployment
```

---

## ⚙️ Prerequisites

Make sure the Jenkins agent (`agent1`) has the following installed:

* Java 11+
* Docker & Docker Compose
* Jenkins
* Trivy
* Gitleaks
* SonarQube Scanner
* Python 3
* Git

---

## 🔐 Tool Installation & Setup

### 1️⃣ Jenkins Setup

* Install Jenkins on a VM or server
* Install required plugins:

  * Git Plugin
  * Docker Pipeline
  * SonarQube Scanner
  * Email Extension Plugin
  * Credentials Binding Plugin

---

### 2️⃣ SonarQube Setup

#### Install SonarQube

```bash
docker run -d --name sonarqube \
-p 9000:9000 sonarqube:lts
```

Access SonarQube:

```
http://<server-ip>:9000
```

Default credentials:

```
username: admin
password: admin
```

#### Configure Jenkins

* Go to **Manage Jenkins → Configure System**

* Add **SonarQube Server**

  * Name: `sonar`
  * Server URL: `http://localhost:9000`
  * Authentication Token: SonarQube token

* Go to **Global Tool Configuration**

  * Add **SonarQube Scanner**
  * Name: `sonar`

---

### 3️⃣ Trivy Installation

```bash
sudo apt-get install wget -y
wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_0.48.0_Linux-64bit.deb
sudo dpkg -i trivy_0.48.0_Linux-64bit.deb
```

Verify:

```bash
trivy --version
```

---

### 4️⃣ Gitleaks Installation

```bash
wget https://github.com/gitleaks/gitleaks/releases/latest/download/gitleaks-linux-amd64
chmod +x gitleaks-linux-amd64
sudo mv gitleaks-linux-amd64 /usr/local/bin/gitleaks
```

Verify:

```bash
gitleaks version
```

---

### 5️⃣ Docker & Docker Compose

```bash
sudo apt install docker.io docker-compose -y
sudo usermod -aG docker jenkins
```

Restart Jenkins after this step.

---

## 🔑 Jenkins Credentials Configuration

Add the following credentials in Jenkins:

### Docker Hub Credentials

* **ID**: `dockerhub-cred`
* Type: Username & Password

### SonarQube Token

* Used in SonarQube Server configuration

---

## 🧪 Security Scans Explained

### 🔍 Gitleaks

* Scans repository for secrets (API keys, passwords)
* Output: `gitleaks-report.txt`
* Build marked **UNSTABLE** if secrets are found

### 🐳 Trivy Filesystem Scan

* Scans project dependencies and config files
* Output: `trivy-report.html`

### 🐳 Trivy Image Scan

* Scans Docker image for vulnerabilities
* Output: `trivy-report-image.html`

### 📊 SonarQube

* Static code analysis
* Enforces Quality Gate before deployment

---

## 🚢 Deployment

The application is deployed using Docker Compose:

```bash
docker compose up -d --build
```

Existing containers are stopped before deployment.

---

## 📧 Email Notifications

On **success or failure**, Jenkins sends an email with attached reports:

* Gitleaks Report
* Trivy FS Scan Report
* Trivy Image Scan Report

---

## 📂 Artifacts Generated

* `gitleaks-report.txt`
* `trivy-report.html`
* `trivy-report-image.html`

These are archived in Jenkins for every build.

---

## 📌 Future Enhancements

* OWASP ZAP Dynamic Application Security Testing
* Slack / Microsoft Teams notifications
* Kubernetes deployment
* Blue-Green or Canary deployments
* Fail builds on Critical vulnerabilities

---

## 👨‍💻 Author

**Aman Yadav**
DevSecOps | Cloud | CI/CD | Docker | Jenkins

---