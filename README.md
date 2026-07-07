# 🚀 CI/CD Pipeline: GitHub → Jenkins → Docker → Amazon ECR → EC2

This project demonstrates a complete CI/CD pipeline using **Jenkins**, **Docker**, **Amazon ECR**, and **Amazon EC2**. Every time code is pushed to GitHub, Jenkins builds a Docker image, pushes it to Amazon ECR, and the image can then be deployed to an EC2 instance.

---

# 📌 Architecture

```
GitHub
   │
   ▼
Jenkins Pipeline
   │
   ▼
Build Docker Image
   │
   ▼
Amazon ECR
   │
   ▼
Amazon EC2
   │
   ▼
Docker Container
   │
   ▼
Live Website
```

---

# 🛠 Technologies Used

- Git & GitHub
- Jenkins
- Docker
- AWS CLI
- Amazon Elastic Container Registry (ECR)
- Amazon EC2
- Nginx
- HTML

---

# 📁 Project Structure

```
.
├── Dockerfile
├── Jenkinsfile
├── index.html
└── README.md
```

---

# 📋 Prerequisites

Before starting, ensure you have:

- AWS Account
- Jenkins Server
- Docker Installed
- AWS CLI Installed
- Git Installed
- GitHub Repository
- Amazon ECR Repository
- Amazon EC2 Instance
- IAM User with ECR Permissions

---

# Step 1: Create an Amazon ECR Repository

Go to:

```
AWS Console
→ Amazon ECR
→ Create Repository
```

Example:

```
Repository Name:
manireet/reetu
```

Repository URI:

```
560696397902.dkr.ecr.us-east-1.amazonaws.com/manireet/reetu
```

---

# Step 2: Install Required Software on Jenkins Server

### Install Git

```bash
sudo yum install git -y
```

or

```bash
sudo apt install git -y
```

---

### Install Docker

```bash
sudo yum install docker -y
```

Start Docker

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Add Jenkins user to Docker group

```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

Verify

```bash
docker --version
docker ps
```

---

### Install AWS CLI

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o awscliv2.zip

unzip awscliv2.zip

sudo ./aws/install
```

Verify

```bash
aws --version
```

---

# Step 3: Install Jenkins Plugins

Go to

```
Manage Jenkins
→ Plugins
```

Install:

- Git
- Docker Pipeline
- Pipeline
- Credentials Binding
- AWS Credentials
- Pipeline AWS Steps

Restart Jenkins.

---

# Step 4: Add AWS Credentials

Go to

```
Manage Jenkins
→ Credentials
→ System
→ Global Credentials
```

Add Credentials

Kind:

```
AWS Credentials
```

ID

```
aws-ecr
```

Save.

---

# Step 5: Create Pipeline Job

```
Dashboard
→ New Item
→ Pipeline
```

Choose

```
Pipeline Script from SCM
```

SCM

```
Git
```

Repository URL

```
https://github.com/<username>/<repository>.git
```

Branch

```
main
```

Script Path

```
Jenkinsfile
```

---

# Step 6: Dockerfile

```dockerfile
FROM nginx:alpine

COPY . /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

---

# Step 7: Jenkinsfile

Create a file named:

```
Jenkinsfile
```

The pipeline performs the following stages:

- Build Docker Image
- Login to Amazon ECR
- Tag Docker Image
- Push Image to Amazon ECR
- Clean Up Docker Images

---

# Step 8: Push Code

```bash
git add .

git commit -m "Initial Commit"

git push origin main
```

---

# Step 9: Build Jenkins Pipeline

Click

```
Build Now
```

Pipeline Stages

```
✔ Checkout

✔ Build Docker Image

✔ Login to Amazon ECR

✔ Tag Image

✔ Push Image

✔ Cleanup
```

---

# Step 10: Verify Image in Amazon ECR

Go to

```
AWS Console

↓

Amazon ECR

↓

Repository

↓

Images
```

You should see

```
latest

1

2

3
```

depending on the Jenkins build number.

---

# Step 11: Launch Amazon EC2

Launch an EC2 instance with:

- Amazon Linux 2 / Ubuntu
- Security Group

Allow

| Type | Port |
|------|------|
| SSH | 22 |
| HTTP | 80 |
|COSTOM PORT | 8080|

Install Docker

```bash
sudo yum install docker -y

sudo systemctl start docker

sudo systemctl enable docker
```

---

# Step 12: Login to Amazon ECR from EC2

```bash
aws ecr get-login-password --region us-east-1 \
| docker login \
--username AWS \
--password-stdin 560696397902.dkr.ecr.us-east-1.amazonaws.com
```

---

# Step 13: Pull Docker Image

```bash
docker pull 560696397902.dkr.ecr.us-east-1.amazonaws.com/manireet/reetu:latest
```

---

# Step 14: Run Docker Container

```bash
docker run -d \
--name website \
-p 80:80 \
560696397902.dkr.ecr.us-east-1.amazonaws.com/manireet/reetu:latest
```

Verify

```bash
docker ps
```

---

# Step 15: Access the Website

Open your browser

```
http://<EC2-Public-IP>
```

Example

```
http://54.xx.xx.xx
```

The website should load successfully.

---

# Troubleshooting

## Docker Permission Denied

```bash
sudo usermod -aG docker jenkins

sudo systemctl restart jenkins
```

---

## AWS Authentication Error

Verify Jenkins AWS Credentials.

---

## ECR Login Failed

Verify:

- AWS CLI Installed
- IAM Permissions
- Repository Exists

---

## Website Not Accessible

Check:

- EC2 Running
- Docker Container Running
- Port 80 Open
- Security Group Allows HTTP
- Container Exposes Port 80

---

# IAM Permissions

Required IAM Policy

```
AmazonEC2ContainerRegistryPowerUser
```

or

```
AmazonEC2ContainerRegistryFullAccess
```

---

# Future Improvements

- GitHub Webhooks
- Automatic Deployment to EC2
- Blue/Green Deployment
- Docker Compose
- Kubernetes (EKS)
- Terraform
- SonarQube
- Trivy Image Scanning
- Slack Notifications

---

# Author

**Manish Kumar**

GitHub: https://github.com/manireet0607

---

# License

This project is for learning and educational purposes.
