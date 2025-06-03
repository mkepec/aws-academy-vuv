# 📦 AWS Academy Guide: Amazon ECR Container Registry Setup

## 📚 Learning Objectives
By the end of this guide, you will:
- ✅ Understand container registry concepts and namespaces
- ✅ Create ECR repositories for frontend and backend applications
- ✅ Configure AWS CLI for ECR authentication
- ✅ Build and push Docker images from local computer or EC2
- ✅ Pull and run images from ECR repositories
- ✅ Manage container images through CLI and console

---

## ⏱️ **Duration**: 30 minutes
## 💰 **Budget Impact**: Minimal (ECR storage ~$0.10/GB/month)
## 🌍 **Region**: us-east-1 (N. Virginia) - **Required for AWS Academy**

---

## 🔑 Prerequisites
- ✅ AWS Academy Learner Lab session started
- ✅ Access to AWS Console through "AWS" button
- ✅ Docker working locally OR existing EC2 instance
- ✅ Your project repository with working Dockerfile
- ✅ Basic understanding of Docker commands

**📝 Note for EC2 Users**: Amazon Linux 2023 uses `dnf` package manager (not `yum`). All commands in this guide reflect the current version.

---

## 📋 Quick Start Summary

1. **Create ECR repositories** for your project components
2. **Configure AWS CLI** with Learner Lab credentials  
3. **Choose build method** (local computer or EC2)
4. **Build and push** Docker images to ECR
5. **Verify and test** image deployment

---

## 🏗️ Part 1: Understanding Container Registries and Namespaces

### **What is Amazon ECR?**
Amazon Elastic Container Registry (ECR) is AWS's **managed Docker registry service** - think of it as your private "Docker Hub" in the cloud.

### **Why Use Namespaces?**
**Namespaces** help organize your container images logically:

```
Your Account Registry: 123456789012.dkr.ecr.us-east-1.amazonaws.com/
├── my-ecommerce-app/frontend    ← Your project name here!
├── my-ecommerce-app/backend     ← Use YOUR actual project name
├── my-ecommerce-app/database    ← Not "animal-rescue"
└── team-blog-app/web-server     ← Different project namespace
```

**🚨 Important**: Replace `animal-rescue` with **your actual project name** throughout this guide!

**Examples of good project names:**
- `student-portal` (for a student management system)
- `ecommerce-shop` (for an online store)
- `task-manager` (for a todo application)
- `social-media-app` (for a social platform)
- `inventory-system` (for warehouse management)

**Benefits:**
- ✅ **Organization**: Group related images together
- ✅ **Team Collaboration**: Multiple people can work on same project
- ✅ **Version Management**: Tag different versions (latest, v1.0.0, dev)
- ✅ **Security**: Control access per repository
- ✅ **Future-Proof**: Easy to add more components later

---

## 📦 Part 2: Create ECR Repositories

### **Step 2.1: Navigate to ECR Service**

1. **Start your AWS Academy session** and click the "AWS" button
2. **Search** for "ECR" in the AWS Console search bar
3. **Click** "Elastic Container Registry"
4. **Verify region**: Ensure you're in **us-east-1** (top-right corner)

### **Step 2.2: Create Frontend Repository**

1. **Click** the orange "Create repository" button

2. **Repository configuration:**
   - **Visibility settings**: Private ✅ (default)
   - **Repository name**: `YOUR-PROJECT-NAME/frontend`
     - **🚨 REPLACE** `YOUR-PROJECT-NAME` with your actual project name!
     - **Examples**: `ecommerce-shop/frontend`, `student-portal/frontend`, `task-manager/frontend`
     - **Keep** `/frontend` for the component name
   - **Tag immutability**: Disabled (allows overwriting tags)
   - **Image scan settings**: ✅ Enable "Scan on push"
   - **Encryption settings**: AES-256 (default)

3. **Click** "Create repository"

### **Step 2.3: Create Backend Repository**

1. **Click** "Create repository" again
2. **Repository configuration:**
   - **Repository name**: `YOUR-PROJECT-NAME/backend`
   - **🚨 Use the SAME project name** as your frontend repository!
   - **Keep all other settings the same**
3. **Click** "Create repository"

### **Step 2.4: Record Your Repository URIs**

You should now see two repositories. **Copy and save** these URIs:

```bash
# Your repositories (replace 123456789012 with your actual AWS account ID)
# Replace YOUR-PROJECT-NAME with your actual project name
Frontend: 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend
Backend:  123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/backend

# Example with actual project name:
Frontend: 123456789012.dkr.ecr.us-east-1.amazonaws.com/ecommerce-shop/frontend
Backend:  123456789012.dkr.ecr.us-east-1.amazonaws.com/ecommerce-shop/backend
```

**✅ Verification**: You should see 2 repositories in your ECR console with "0 images" in each.

---

## 🔧 Part 3: AWS CLI Configuration for ECR

### **Step 3.1: Install AWS CLI (if needed)**

**For Windows Users:**

**Option 1: Download MSI Installer (Recommended)**
```bash
# 1. Download from: https://awscli.amazonaws.com/AWSCLIV2.msi
# 2. Run the installer
# 3. Open Command Prompt or PowerShell
# 4. Verify: aws --version
```

**Option 2: Using Chocolatey**
```bash
# First install Chocolatey (if not installed):
# 1. Open PowerShell as Administrator
# 2. Run: Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
# 3. Close and reopen PowerShell as Administrator
# 4. Install AWS CLI:
choco install awscli

# Verify installation
aws --version
```

**Option 3: Using WSL (Windows Subsystem for Linux)**
```bash
# If you have WSL installed, use the Linux instructions below
# Open WSL terminal and follow Linux installation steps
```

**For Mac Users:**
```bash
# Using Homebrew (recommended)
brew install awscli

# Or download installer:
# https://awscli.amazonaws.com/AWSCLIV2.pkg

# Verify installation
aws --version
```

**For Linux/WSL Users:**
```bash
# Using curl
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Or using package manager (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install awscli -y

# Verify installation
aws --version
```

**For EC2 Instance:**
```bash
# AWS CLI is pre-installed on Amazon Linux 2023
aws --version

# If needed, install with:
sudo dnf install awscli -y
```

### **Step 3.2: Get AWS Academy Credentials**

1. **In your Learner Lab interface**, click "AWS Details" button
2. **Copy the credentials** - you'll see something like:

```bash
[default]
aws_access_key_id=ASIAIOSFODNN7EXAMPLE
aws_secret_access_key=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
aws_session_token=AQoEXAMPLEH4aoAH0gNCAPy...
```

### **Step 3.3: Configure AWS CLI**

**Option A: Using aws configure (Interactive)**
```bash
aws configure set aws_access_key_id YOUR_ACCESS_KEY_ID
aws configure set aws_secret_access_key YOUR_SECRET_ACCESS_KEY  
aws configure set aws_session_token YOUR_SESSION_TOKEN
aws configure set region us-east-1
```

**Option B: Using credentials file**

**For Windows:**
```bash
# Create AWS directory and credentials file
mkdir "%USERPROFILE%\.aws"

# Create credentials file (use Notepad or text editor)
# File location: C:\Users\YourUsername\.aws\credentials
# Content:
[default]
aws_access_key_id=YOUR_ACCESS_KEY_ID
aws_secret_access_key=YOUR_SECRET_ACCESS_KEY
aws_session_token=YOUR_SESSION_TOKEN

# Create config file
# File location: C:\Users\YourUsername\.aws\config
# Content:
[default]
region=us-east-1
output=json
```

**For Mac/Linux/WSL:**
```bash
# Create/edit credentials file
mkdir -p ~/.aws
cat > ~/.aws/credentials << EOF
[default]
aws_access_key_id=YOUR_ACCESS_KEY_ID
aws_secret_access_key=YOUR_SECRET_ACCESS_KEY
aws_session_token=YOUR_SESSION_TOKEN
EOF

# Set default region
cat > ~/.aws/config << EOF
[default]
region=us-east-1
output=json
EOF
```

### **Step 3.4: Test AWS CLI Configuration**

```bash
# Test basic connectivity
aws sts get-caller-identity

# Expected output (with your actual account ID):
# {
#     "UserId": "AROAEXAMPLE...",
#     "Account": "123456789012",
#     "Arn": "arn:aws:sts::123456789012:assumed-role/LabRole/..."
# }

# Test ECR access
aws ecr describe-repositories --region us-east-1
```

**✅ Verification**: You should see JSON output with your ECR repositories listed.

---

## 🚀 Part 4: Build and Push Options

You have **two options** for building and pushing your Docker images:

### **Option A: Build on Local Computer** 
- ✅ **Pros**: Use your familiar development environment
- ✅ **Pros**: No EC2 costs while building
- ❌ **Cons**: Need Docker and AWS CLI installed locally
- ❌ **Cons**: Upload time depends on internet speed

### **Option B: Build on EC2 Instance**
- ✅ **Pros**: Fast upload to ECR (same AWS network)
- ✅ **Pros**: No local Docker installation needed
- ✅ **Pros**: Consistent build environment
- ❌ **Cons**: Requires EC2 instance (covered in separate guide)
- ❌ **Cons**: EC2 compute costs during build

---

## 💻 Option A: Build and Push from Local Computer

### **Step A.1: Get ECR Login Command**

1. **In ECR Console**, select your `animal-rescue/frontend` repository
2. **Click** "View push commands" button (top-right)
3. **Copy the first command** (ECR login):

```bash
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com
```

4. **Run this command** in your local terminal

**Expected output:**
```
Login Succeeded
```

### **Step A.2: Build and Tag Image Locally**

```bash
# Navigate to your project directory (containing Dockerfile)
cd /path/to/your/project

# Build your Docker image
docker build -t animal-frontend-app .

# Tag for ECR (replace with your account ID)
docker tag animal-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/animal-rescue/frontend:latest

# Optional: Tag with version number
docker tag animal-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/animal-rescue/frontend:v1.0.0
```

### **Step A.3: Push to ECR**

```bash
# Push latest tag
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Push version tag (if created)
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:v1.0.0
```

### **Step A.4: Version Management Strategy**

**When you update your code and need a new Docker image:**

```bash
# Option 1: Update 'latest' tag (simple, for development)
docker build -t my-frontend-app .
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Option 2: Use version numbers (recommended for production)
docker build -t my-frontend-app .
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:v1.1.0
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:v1.1.0
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Option 3: Use git commit hash (for tracking exact code version)
GIT_HASH=$(git rev-parse --short HEAD)
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:$GIT_HASH
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:$GIT_HASH
```

**Tagging Best Practices:**
- ✅ **latest**: Always points to newest version
- ✅ **v1.0.0**: Specific version for rollback capability  
- ✅ **dev/staging/prod**: Environment-specific tags
- ✅ **commit-hash**: Exact code tracking

---

## 🖥️ Option B: Build and Push from EC2 Instance

**Prerequisites**: You have an EC2 instance running (covered in separate EC2 guide)

### **Step B.1: Connect to Your EC2 Instance**

**SSH Connection Instructions:**

**For Windows Users:**
```bash
# Using PowerShell or Command Prompt
ssh -i labsuser.pem ec2-user@YOUR_EC2_PUBLIC_IP

# Using PuTTY:
# 1. Download labsuser.ppk from "AWS Details" 
# 2. Host Name: ec2-user@YOUR_EC2_PUBLIC_IP
# 3. Connection → SSH → Auth → Browse for labsuser.ppk
# 4. Click Open
```

**For Mac/Linux/WSL Users:**
```bash
# Make key file secure (first time only)
chmod 400 labsuser.pem

# Connect to instance
ssh -i labsuser.pem ec2-user@YOUR_EC2_PUBLIC_IP
```

### **Step B.2: Prepare EC2 Environment**

**Once connected to your EC2 instance**, run:

```bash
# Update system packages (Amazon Linux 2023 uses dnf)
sudo dnf update -y

# Install Docker
sudo dnf install docker -y
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -a -G docker ec2-user

# Install Git
sudo dnf install git -y

# Log out and back in for docker group changes
exit
# SSH back in using same command as above
```

### **Step B.2: Configure AWS CLI on EC2**

**Good news**: If your EC2 has the **LabInstanceProfile** attached, AWS CLI should work automatically!

```bash
# Test if credentials are working
aws sts get-caller-identity

# If working, you'll see your account info
# If not working, configure manually using credentials from "AWS Details"
```

### **Step B.4: Clone and Build on EC2**

```bash
# Clone your repository
git clone YOUR_REPOSITORY_URL
cd YOUR_PROJECT_DIRECTORY

# Verify Dockerfile exists
ls -la Dockerfile

# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Build image
docker build -t my-frontend-app .

# Tag for ECR (replace YOUR-PROJECT-NAME with your actual project name)
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Push to ECR
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest
```

### **Step B.5: Handle Code Updates on EC2**

**When you update your code and want to rebuild:**

```bash
# Pull latest code changes
git pull origin main

# Rebuild with new version tag
docker build -t my-frontend-app .

# Tag with version number and latest
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:v1.1.0
docker tag my-frontend-app:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Push both tags
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:v1.1.0
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest
```

---

## 🔍 Part 5: Verify and Manage Images

### **Step 5.1: List Images via CLI**

```bash
# List all images in frontend repository (replace YOUR-PROJECT-NAME)
aws ecr list-images --repository-name YOUR-PROJECT-NAME/frontend --region us-east-1

# Get detailed image information
aws ecr describe-images --repository-name YOUR-PROJECT-NAME/frontend --region us-east-1

# Example with actual project name:
aws ecr list-images --repository-name ecommerce-shop/frontend --region us-east-1
```

### **Step 5.2: Verify in AWS Console**

1. **Go to ECR Console**
2. **Click** on your `YOUR-PROJECT-NAME/frontend` repository
3. **Refresh** the page if needed
4. **You should see** your uploaded image with:
   - Image URI
   - Image tags (latest, v1.0.0, etc.)
   - Image size
   - Push date
   - Vulnerability scan results

### **Step 5.3: Pull and Test Image**

**From any machine with Docker and ECR access:**

```bash
# Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# Pull the image (replace YOUR-PROJECT-NAME)
docker pull 123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Run the container (example for web application)
docker run -d \
    --name my-frontend \
    -p 80:80 \
    123456789012.dkr.ecr.us-east-1.amazonaws.com/YOUR-PROJECT-NAME/frontend:latest

# Check if running
docker ps
```

**⚠️ Important**: If running on EC2, ensure **port 80** is open in your security group!

---

## 🛡️ Part 6: Security Group Configuration (For Docker Run)

**If you plan to run containers on EC2**, ensure proper port access:

### **Common Port Requirements:**

| Application Type | Port | Protocol | Source |
|------------------|------|----------|---------|
| **Web Frontend** | 80 | TCP | 0.0.0.0/0 |
| **HTTPS Frontend** | 443 | TCP | 0.0.0.0/0 |
| **Development Server** | 3000 | TCP | 0.0.0.0/0 |
| **API Backend** | 8080 | TCP | 0.0.0.0/0 |
| **Database** | 5432/3306 | TCP | VPC only |

### **Quick Security Group Update:**

1. **EC2 Console** → **Security Groups**
2. **Select** your instance's security group  
3. **Edit inbound rules** → **Add rule**
4. **Type**: HTTP, **Port**: 80, **Source**: Anywhere-IPv4
5. **Save rules**

---

## 📊 Part 7: Image Management Best Practices

### **Tagging Strategy**

```bash
# Use semantic versioning
docker tag my-app:latest ECR_URI:v1.0.0
docker tag my-app:latest ECR_URI:v1.0
docker tag my-app:latest ECR_URI:latest

# Use environment tags
docker tag my-app:latest ECR_URI:dev
docker tag my-app:latest ECR_URI:staging  
docker tag my-app:latest ECR_URI:prod
```

### **Repository Lifecycle Management**

```bash
# Delete old image versions to save costs (replace YOUR-PROJECT-NAME)
aws ecr batch-delete-image \
    --repository-name YOUR-PROJECT-NAME/frontend \
    --image-ids imageTag=old-version \
    --region us-east-1

# List image sizes to monitor storage costs
aws ecr describe-images \
    --repository-name YOUR-PROJECT-NAME/frontend \
    --query 'imageDetails[*].[imageTags[0],imageSizeInBytes]' \
    --output table
```

---

## 💰 Budget Considerations

### **ECR Costs:**
- **Storage**: ~$0.10 per GB per month
- **Data Transfer**: Free within same region
- **Scanning**: Free tier: 100 scans per month

### **Cost Optimization Tips:**
```bash
# Monitor repository sizes
aws ecr describe-repositories --query 'repositories[*].[repositoryName,repositorySizeInBytes]' --output table

# Set up lifecycle policies (optional)
# Automatically delete old images after X days
```

---

## 🐛 Troubleshooting

### **Common Issues:**

| Problem | Symptoms | Solution |
|---------|----------|----------|
| **Authentication Failed** | `docker login` fails | Re-run ECR login command |
| **Permission Denied** | Push/pull fails | Check LabRole is attached to EC2 |
| **Wrong Region** | Repository not found | Verify us-east-1 region |
| **Build Fails** | Docker build errors | Check Dockerfile syntax |
| **Session Expired** | CLI commands fail | Get new credentials from "AWS Details" |

### **Debug Commands:**

```bash
# Check AWS CLI configuration
aws configure list

# Test ECR connectivity
aws ecr describe-repositories

# Check Docker login status
docker info | grep Registry

# View local Docker images
docker images | grep ECR_URI
```

---

## ✅ Verification Checklist

**After completing this guide, you should have:**

- [ ] Two ECR repositories created (frontend/backend)
- [ ] AWS CLI configured with Learner Lab credentials
- [ ] Successfully logged into ECR
- [ ] Built and tagged Docker image locally or on EC2
- [ ] Pushed image to ECR repository
- [ ] Verified image appears in AWS Console
- [ ] Successfully pulled image from ECR
- [ ] (If testing) Container runs and is accessible

---

## 🔗 Next Steps

### **Completed ECR Setup? Choose your path:**

**Frontend Deployment:**
- **Next Guide**: "Frontend Deployment on EC2"
- **You'll use**: Images from ECR repositories you just created

**Backend Development:**
- **Create**: Backend Docker image using same process
- **Push**: To `animal-rescue/backend` repository

**Database Integration:**
- **Next Guide**: "RDS Database Setup" 
- **Connect**: Backend containers to managed database

---

## 🎯 Summary

**What you accomplished:**
- ✅ **Understanding**: Container registries and namespace organization
- ✅ **Infrastructure**: ECR repositories for your project
- ✅ **Security**: AWS CLI configuration with Learner Lab credentials
- ✅ **Skills**: Building and pushing container images
- ✅ **Verification**: Image management and testing

**Your project now has:**
- Private container registry in AWS
- Organized image storage with namespaces
- Foundation for scalable deployment architecture
- Skills for container image lifecycle management

**Ready for production deployment!** 🚀
