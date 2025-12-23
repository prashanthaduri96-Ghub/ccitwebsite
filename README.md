#!/bin/bash
# AWS CodeDeploy Agent Install Script
# Works on Amazon Linux, Ubuntu, RHEL

set -e

# Detect OS
OS=$(uname -s)
DISTRO=$(cat /etc/os-release | grep -w ID | cut -d= -f2)

echo "Installing CodeDeploy agent on $DISTRO ($OS)..."

# Update packages
sudo yum update -y || sudo apt-get update -y

# Install Ruby (required for CodeDeploy agent)
if [[ "$DISTRO" == "ubuntu" ]]; then
    sudo apt-get install -y ruby wget
else
    sudo yum install -y ruby wget
fi

# Download CodeDeploy agent installer
REGION="ap-south-1"   # Change to your AWS region
cd /home/ec2-user || cd /tmp
wget https://aws-codedeploy-$REGION.s3.$REGION.amazonaws.com/latest/install

# Make installer executable
chmod +x ./install

# Run installer
sudo ./install auto

# Enable & start service
sudo systemctl enable codedeploy-agent
sudo systemctl start codedeploy-agent

# Verify status
sudo systemctl status codedeploy-agent

