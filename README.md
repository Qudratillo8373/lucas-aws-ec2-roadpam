# lucas-aws-ec2-roadpam

# AWS EC2 Static Website Deployment (Ubuntu + Nginx)

This repository documents the steps to deploy a static website on an AWS EC2 instance using Ubuntu Server, Nginx, and optionally HTTPS using Let’s Encrypt with a free domain.

---

## ✅ Project Overview

This project demonstrates how to:

- Launch an AWS EC2 instance (Ubuntu Server)
- Configure security group for SSH and HTTP
- Connect via SSH using a key pair
- Install Nginx web server
- Deploy a static HTML website
- (Optional) Setup free domain and HTTPS using Let’s Encrypt

---

## 🚀 Step-by-Step Guide

### Step 1: Launch EC2 Instance

1. Open AWS Console → EC2 → Launch Instance
2. Choose **Ubuntu Server AMI**
3. Select **t2.micro** (Free Tier eligible)
4. Use **default VPC and subnet**
5. Configure **Security Group**:
   - SSH (port 22) from your IP
   - HTTP (port 80) from anywhere (0.0.0.0/0)
6. Create or use an existing key pair for SSH
7. Ensure **Public IP** is enabled

---

REMAINDER: If you don't have a public key for key-pair run this command and just hit 'ENTER' or "RETURN' 4 times. NOTE: Please do not press any keyboard 

```
ssh-keygen
```

### Step 2: Connect to EC2 via SSH

From your Ubuntu machine:

```
ssh -i ~/.ssh/id_ed25519 ubuntu@<EC2_PUBLIC_IP>
```
Step 3: Update System Packages
```
sudo apt update
sudo apt upgrade -y
```
Step 4: Install Nginx
```
sudo apt install nginx -y

sudo systemctl start nginx
sudo systemctl enable nginx
```
Step 5: Deploy Static Website
```
sudo nano /var/www/html/index.html
```

```
<!DOCTYPE html>
<html>
<head>
    <title>My Static Website</title>
</head>
<body>
    <h1>Welcome to my EC2 website!</h1>
    <p>This website is hosted on AWS EC2 using Nginx.</p>
</body>
</html>

```
Step 6: Verify Website via Public IP

```
http://<EC2_PUBLIC_IP>

```

Route 53 Domain Setup
🎯 Step 7: Register Domain (or use existing)

   1. Go to AWS Console → Route 53

   2. Click Registered domains

   3. Register a new domain or use an existing one

Step 8: Create Hosted Zone

   1. Go to Route 53 → Hosted zones

   2. Click Create hosted zone

   3. Enter your domain name

   4. Choose Public hosted zone

Step 9: Add DNS Record (A Record)

   1. Open your hosted zone

   2. Click Create record
   
   3. Choose Record type: A
   
   4. Name: @
   
   5. Value: <EC2_PUBLIC_IP>

   6. Click Create records


HTTPS Setup with Let’s Encrypt
✅ Step 10: Install Certbot using Snap

```
sudo apt update
sudo apt install snapd -y
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Step 11: Issue SSL Certificate

```
sudo certbot --nginx
```
   Follow prompts:

   1. Enter email

   2. Agree to terms

   3. Select your domain

   4. Enable redirect from HTTP → HTTPS


Final Access:

Your website should now be accessible via:

```
https://yourdomain.com

```

Notes & Troubleshooting
❌ “certbot package not found”

Install Certbot using Snap (shown above).

❌ “Let’s Encrypt does not issue certificates for IP”

You must use a domain name.

❌ DNS not working

Wait 5–10 minutes for DNS propagation.


Step 12: Prepare EC2 for CI/CD

Create deployment directory (already default for Nginx):

```
sudo chown -R ubuntu:ubuntu /var/www/html

```

Step 13: Add GitHub Secrets

In your GitHub repository:

Settings → Secrets → Actions → New Repository Secret

Add:
```
Name 	            Value
EC2_HOST	         Your EC2 Public IP
EC2_USER	         ubuntu
EC2_SSH_KEY	         Private SSH key (content of id_ed25519)
```
⚠️ Paste the private key content, not the filename.

Step 14: GitHub Actions Workflow

Create this file in your repo:

```
.github/workflows/deploy.yml
```

```
name: Deploy Static Website to EC2

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v4

    - name: Deploy to EC2
      uses: appleboy/ssh-action@v1.0.3
      with:
        host: ${{ secrets.EC2_HOST }}
        username: ${{ secrets.EC2_USER }}
        key: ${{ secrets.EC2_SSH_KEY }}
        script: |
          sudo rm -rf /var/www/html/*
          sudo cp -r * /var/www/html/
          sudo systemctl reload nginx
```

Step 15: Test CI/CD

   1. Modify index.html
   
   2. Commit & push to main
   
   3. GitHub Actions runs automatically
   
   4. Website updates live 🎉


