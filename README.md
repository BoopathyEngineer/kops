Kubernetes Cluster Deployment Using Kops
Overview
This repository contains the setup and configuration for deploying a Kubernetes cluster on AWS using Kops.

Project Highlights
Set up a multi-zone Kubernetes cluster
Configured Kops state store on S3
Launched nodes with auto-scaling
Integrated SSH for secure access
Validated cluster functionality
Prerequisites
AWS CLI installed & configured
Kops & Kubectl installed
A registered domain (e.g., boopathy98.xyz)
An S3 bucket for Kops state storage
Commands Used
1️⃣ Create Cluster
sh
Copy
Edit
kops create cluster --name=boopathy98.xyz \
--state=s3://boopathy-devops-cloud \
--zones=us-east-1a,us-east-1b \
--node-count=2 --control-plane-count=1 \
--node-size=t3.medium --control-plane-size=t3.medium \
--control-plane-zones=us-east-1a \
--control-plane-volume-size=10 --node-volume-size=10 \
--ssh-public-key ~/.ssh/id_ed25519.pub --dns-zone=boopathy98.xyz
2️⃣ Update Cluster
sh
Copy
Edit
kops update cluster --name=boopathy98.xyz --state=s3://boopathy-devops-cloud --yes
3️⃣ Validate Cluster
sh
Copy
Edit
kops validate cluster --name=boopathy98.xyz --state=s3://boopathy-devops-cloud
Future Enhancements
Deploy workloads and microservices
Implement monitoring with Prometheus & Grafana
Automate deployments with CI/CD
