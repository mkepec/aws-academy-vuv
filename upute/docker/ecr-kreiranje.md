# Prerequisite: Creating ECR Repositories via AWS Console

## Overview
This short tutorial teaches you how to create and manage ECR repositories using the AWS Console interface. Complete this before starting your Docker containerization projects.

**Time Required**: 10-15 minutes  
**Prerequisites**: Active AWS Academy lab session

---

## Step 1: Access AWS Console and Navigate to ECR

### 1.1 Start Your AWS Academy Lab
1. **Login to AWS Academy** and start your lab session
2. **Click the "AWS" button** to open AWS Management Console
3. **Wait for the console to fully load**

### 1.2 Navigate to ECR Service
1. **In the search bar at the top**, type `ECR`
2. **Click on "Elastic Container Registry"** from the dropdown
3. **Alternative method**: Go to **Services** → **Containers** → **Elastic Container Registry**

### 1.3 Understand the ECR Dashboard
You should now see the ECR main page with:
- **Private repositories**: Your private container repositories (empty initially)
- **Public repositories**: For open-source projects (we won't use this)
- **Create repository** button

---

## Step 2: Create Your First ECR Repository

### 2.1 Start Repository Creation
1. **Click the "Create repository" button**
2. You'll see the **Create repository** page with several options

### 2.2 Configure Repository Settings

**Visibility Settings**:
- ✅ Select **"Private"** (this should be selected by default)
- Leave "Public" unchecked

**Repository Configuration**:
1. **Repository name**: Enter `my-first-repo`
   - Use lowercase letters, numbers, hyphens, underscores, periods only
   - No spaces or special characters

**Advanced Settings** (leave these as default for now):
- **Tag immutability**: Disabled
- **Image scan settings**: ✅ Check **"Scan on push"** (recommended)
- **Encryption settings**: AES-256 (default)

### 2.3 Create the Repository
1. **Click "Create repository"** button at the bottom
2. **Wait a few seconds** for creation to complete
3. **You should see a success message** and be redirected to your repository

---

## Step 3: Verify Your Repository

### 3.1 Repository Details
After creation, you should see:
- ✅ **Repository name**: my-first-repo
- 📊 **Repository URI**: Something like `123456789012.dkr.ecr.us-east-1.amazonaws.com/my-first-repo`
- 🔒 **Visibility**: Private
- 📈 **Images**: 0 (empty repository)

### 3.2 Important Information to Save
**Copy and save the Repository URI** - you'll need this later for pushing Docker images:
```
123456789012.dkr.ecr.us-east-1.amazonaws.com/my-first-repo
```

---

## Step 4: Create Additional Repositories (Practice)

Let's create repositories for a typical web application project:

### 4.1 Create Frontend Repository
1. **Click "Create repository"** again (or go back to ECR main page)
2. **Repository name**: `student-frontend`
3. **Keep all other settings the same**
4. **Click "Create repository"**

### 4.2 Create Backend Repository
1. **Create another repository**
2. **Repository name**: `student-backend`
3. **Click "Create repository"**

### 4.3 Create Database Repository
1. **Create one more repository**
2. **Repository name**: `student-database`
3. **Click "Create repository"**

---

## Step 5: View and Manage Your Repositories

### 5.1 Repository List View
1. **Go back to ECR main page** (click "Repositories" in the left sidebar)
2. **You should now see all your repositories**:
   - my-first-repo
   - student-frontend
   - student-backend
   - student-database

### 5.2 Repository Information
For each repository, you can see:
- **Repository name**
- **Repository URI** (click to copy)
- **Images count** (0 for all new repositories)
- **Tag mutability**
- **Scan on push** status

### 5.3 Access Individual Repository
1. **Click on any repository name** (e.g., "my-first-repo")
2. **You'll see the repository details page** with:
   - Images tab (empty for now)
   - Permissions tab
   - Lifecycle policy tab
   - **"View push commands"** button (important for later!)

---

## Step 6: Get Push Commands (For Future Use)

### 6.1 View Push Commands
1. **Click on one of your repositories** (e.g., "student-frontend")
2. **Click "View push commands"** button in the top right
3. **A modal will open** showing 4 commands you'll use later

### 6.2 Understanding the Push Commands
The commands shown are:
```bash
# 1. Login to ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 123456789012.dkr.ecr.us-east-1.amazonaws.com

# 2. Build Docker image
docker build -t student-frontend .

# 3. Tag image for ECR
docker tag student-frontend:latest 123456789012.dkr.ecr.us-east-1.amazonaws.com/student-frontend:latest

# 4. Push image to ECR
docker push 123456789012.dkr.ecr.us-east-1.amazonaws.com/student-frontend:latest
```

**Note**: Don't run these commands yet - you'll use them in the next tutorial when you have Docker images ready!

---

## Step 7: Repository Management Tips

### 7.1 Naming Best Practices
✅ **Good repository names**:
- `my-react-app`
- `ecommerce-frontend`
- `student-portfolio-api`
- `todo-backend`

❌ **Avoid these names**:
- `MyApp` (uppercase)
- `my_app_v1.0` (versions in name)
- `test123` (not descriptive)
- `my app` (spaces)

### 7.2 Organization Strategy
For projects with multiple components:
- `projectname-frontend`
- `projectname-backend`
- `projectname-database`
- `projectname-nginx`

### 7.3 Repository Settings to Remember
- **Always use Private repositories** for your projects
- **Enable "Scan on push"** for security
- **Save Repository URIs** - you'll need them for Docker operations

---

## Verification Checklist

Before proceeding to the Docker tutorials, make sure you have:

- [ ] ✅ Successfully created at least one ECR repository
- [ ] ✅ Can see your repositories in the ECR console
- [ ] ✅ Saved the Repository URI for at least one repository
- [ ] ✅ Viewed the push commands (even if you didn't run them)
- [ ] ✅ Repository shows "Scan on push: Enabled"

---

## What's Next?

Now that you have ECR repositories created, you're ready for:

1. **Dockerizing your applications** (React, Vue.js, or .NET)
2. **Building Docker images** locally
3. **Pushing images to your ECR repositories**

Your ECR repositories are now ready to receive Docker images! 🐳

---

## Quick Reference

### Key Information to Save:
- **Repository Names**: my-first-repo, student-frontend, student-backend, student-database
- **Repository URIs**: `123456789012.dkr.ecr.us-east-1.amazonaws.com/[repository-name]`
- **Region**: us-east-1 (or your lab's region)

### Where to Find Things:
- **ECR Service**: AWS Console → Search "ECR"
- **Repository List**: ECR → Repositories (left sidebar)
- **Push Commands**: Click repository → "View push commands"
- **Repository Details**: Click on repository name
