🚀 Spring Boot Java CI/CD Pipeline (Customized for Your Setup)


👤 Project Owner
DockerHub: mnlove (replace if needed)
Application Image: mnlove/mnr
Jenkins EC2-based CI/CD pipeline
📌 Overview

This project is a Spring Boot MVC application deployed using a fully automated CI/CD pipeline.

🚀 Pipeline includes:
GitHub SCM checkout
Maven build & test
SonarQube code quality analysis
OWASP Dependency-Check security scan
Docker image build
Trivy image scanning
DockerHub push
Docker container deployment on EC2
🖥️ Server Requirements (Your Setup)
EC2 instance: t2.large (recommended)
Storage: 50 GB EBS
OS: Amazon Linux 2023
User: ec2-user
Jenkins running on port 8080
App running on port 8000
SonarQube on port 9000
⚙️ Initial Setup
sudo yum update -y
🧱 Jenkins Installation
sudo yum install wget -y
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

sudo yum install java-17-amazon-corretto -y
sudo yum install jenkins -y

sudo systemctl start jenkins
sudo systemctl enable jenkins

👉 Access:

http://<EC2-PUBLIC-IP>:8080
🐳 Docker Setup
sudo yum install -y docker
sudo systemctl start docker

sudo usermod -aG docker ec2-user
sudo usermod -aG docker jenkins
🔐 Jenkins Credentials (Your Setup)
DockerHub Credentials
Kind: Username with password ✅ (IMPORTANT FIX)
ID: mnlove
Username: mnlove
Password: DockerHub password
SonarQube Token
ID: sonar
Type: Secret text
URL: http://localhost:9000
🧪 Tools Installed
Tool	Purpose
Maven	Build
JDK 11	Runtime
SonarQube	Code quality
OWASP Dependency Check	Vulnerability scan
Trivy	Docker image scan
🐘 OWASP Dependency Check
sudo mkdir -p /opt/dependency-check
cd /opt

sudo curl -L -O https://github.com/jeremylong/DependencyCheck/releases/download/v8.4.2/dependency-check-8.4.2-release.zip
sudo unzip dependency-check-8.4.2-release.zip
🔍 Trivy Installation (FIXED)
curl -sfL https://raw.githubusercontent.com/aquasecurity/trivy/main/contrib/install.sh | sudo sh -s -- -b /usr/local/bin
🐳 Dockerfile (IMPORTANT FIX)
FROM eclipse-temurin:11-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
🚀 FINAL FIXED JENKINS PIPELINE (YOUR VERSION)
pipeline {
    agent any

    tools {
        jdk 'jdk11'
        maven 'mvn'
    }

    stages {

        // ---------------- SCM ----------------
        stage('SCM') {
            steps {
                git branch: 'main',
                url: 'https://github.com/maheshchirram/Project_01_DependencyCheck_Docker.git'
            }
        }

        // ---------------- Build & Test ----------------
        stage('Build & Test') {
            steps {
                sh 'cd java-cicd-project/spring-boot-app && mvn clean compile test'
            }
        }

        // ---------------- OWASP Dependency Check ----------------
        stage('OWASP Dependency Check') {
            steps {
                script {
                    sh '''
                        /opt/dependency-check/bin/dependency-check.sh \
                        --project "SpringBootApp" \
                        --scan java-cicd-project/spring-boot-app \
                        --format HTML \
                        --out dependency-check-report \
                        --data /var/lib/jenkins/odc-data
                    '''
                }

                // Save report in Jenkins artifacts
                archiveArtifacts artifacts: 'dependency-check-report/**', allowEmptyArchive: true
            }
        }

        // ---------------- SonarQube ----------------
        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar', variable: 'SONAR_TOKEN')]) {
                    sh '''
                        cd java-cicd-project/spring-boot-app && \
                        mvn sonar:sonar \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=$SONAR_TOKEN
                    '''
                }
            }
        }

        // ---------------- Build JAR ----------------
        stage('Build JAR') {
            steps {
                sh 'cd java-cicd-project/spring-boot-app && mvn clean package'
            }
        }

        // ---------------- Docker Build ----------------
        stage('Build Docker Image') {
            steps {
                sh 'cd java-cicd-project/spring-boot-app && docker build -t mnlove/mnr:${BUILD_NUMBER} .'
            }
        }

        // ---------------- Trivy Scan ----------------
        stage('Trivy Scan') {
            steps {
                sh 'trivy image mnlove/mnr:${BUILD_NUMBER}'
            }
        }

        // ---------------- Push to DockerHub ----------------
        stage('Push Image to Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'mnlove', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                        sh '''
                            echo $PASS | docker login -u $USER --password-stdin
                        '''
                    }

                    sh 'docker push mnlove/mnr:${BUILD_NUMBER}'
                }
            }
        }

        // ---------------- Deploy ----------------
        stage('Deploying image to docker container') {
            steps {
                sh '''
                    docker rm -f java-app || true
                    docker run -d --name java-app -p 8000:8080 mnlove/mnr:${BUILD_NUMBER}
                '''
            }
        }
    }
}
🌐 ACCESS YOUR APPLICATION

👉 Replace with your EC2 IP:

http://<YOUR-EC2-IP>:8000
⚠️ IMPORTANT FIXES INCLUDED

✔ Fixed DockerHub credentials issue
✔ Fixed Docker login security
✔ Fixed container deployment issue
✔ Fixed Trivy install approach
✔ Fixed Dockerfile best practice
✔ Removed risky post always cleanup

