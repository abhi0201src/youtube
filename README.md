YouTube Application Deployment on AWS EC2
Project Overview
This repository contains a JavaScript-based YouTube application packaged as a Docker container for deployment on AWS EC2 instances.

Prerequisites
Before you begin, ensure you have the following installed:
Docker
AWS CLI (configured with your credentials)
Git
Node.js (if developing locally)

# Application Structure
youtube/

public/          # Static files

src/             # Application source code

Dockerfile       # Docker configuration

package.json     # Node.js dependencies

README.md        # This file

# AWS EC2 Deployment
a. Launch an EC2 Instance

Use Amazon Linux 2 or Ubuntu AMI
Ensure security group allows:
HTTP (port 80)
HTTPS (port 443)
SSH (port 22)
Minimum recommended: t2.micro instance

b. Connect to Your EC2 Instance
bash

ssh -i "your-key.pem" ec2-user@your-ec2-public-dns

c. Install Docker on EC2

d. Deploy Your Application

# On EC2 instance:

git clone https://github.com/abhi0201src/youtube.git

cd youtube

docker build -t youtube-app .

docker run -p 80:3000 -d youtube-app

 Access via Web Browser
Simply enter one of these in your browser's address bar:

[http://your public ip:3000]
