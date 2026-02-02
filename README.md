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

### 1. Launch EC2 Instance

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

### 2. Connect to EC2 via SSH

From your Ubuntu machine:

```
ssh -i ~/.ssh/id_ed25519 ubuntu@<EC2_PUBLIC_IP>

sudo apt update
sudo apt upgrade -y

sudo apt install nginx -y

sudo systemctl start nginx
sudo systemctl enable nginx

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
    <p>This is a static website hosted on AWS EC2 using Nginx.</p>
</body>
</html>
```

Verify Website via Public IP

Open your browser:
```
http://<EC2_PUBLIC_IP>
```

Route 53 Domain Setup
🎯 Step 7: Register Domain (or use existing)

Go to AWS Console → Route 53

Click Registered domains

Register a new domain or use an existing one

   Create Hosted Zone

Go to Route 53 → Hosted zones

Click Create hosted zone

Enter your domain name

Choose Public hosted zone

 Add DNS Record (A Record)

Open your hosted zone

Click Create record

Choose Record type: A

Name: @

Value: <EC2_PUBLIC_IP>

Click Create records


Wait for DNS Propagation

Use:
```
dig yourdomain.com
```

HTTPS Setup with Let’s Encrypt
   Install Certbot using Snap
```
sudo apt update
sudo apt install snapd -y
sudo snap install core
sudo snap refresh core
sudo snap install --classic certbot
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Issue SSL Certificate
```
sudo certbot --nginx
```

Follow prompts:

Enter email

Agree to terms

Select your domain

Enable redirect from HTTP → HTTPS

Final Access

Your website should now be accessible via:
```
https://yourdomain.com
```
