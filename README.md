# Spring Boot CI/CD Pipeline with Jenkins, Trivy & Dependency-Check

<img width="953" height="223" alt="image" src="https://github.com/user-attachments/assets/06f533ec-a7df-441f-abfc-efd9ad5c1e0f" />


## Project Overview
This project demonstrates a CI/CD pipeline for a Spring Boot application using Jenkins with security scanning tools like Trivy and OWASP Dependency-Check.

---

## Tech Stack
- Java / Spring Boot
- Jenkins
- Docker
- Trivy (Container Vulnerability Scanner)
- OWASP Dependency-Check
- SonarQube (optional)

---

## Pipeline Stages

### 1. Checkout Code
Pull source code from GitHub.

### 2. Build Application
```bash
mvn clean package
