# Setting Up Prerequisites to Create an EKS Cluster on Bare Metal or Laptop


## Install Required Tools
1. **AWS CLI**  
   The AWS Command Line Interface (CLI) enables you to interact with AWS services from the command line.

2. **kubectl CLI**  
   `kubectl` is used to manage Kubernetes clusters.

3. **eksctl CLI**  
   `eksctl` is a CLI tool to create, manage, and delete Amazon EKS clusters.

---

## Configuring AWS CLI with Security Credentials

1. **Generate Security Credentials:**
   - Navigate to the **AWS Management Console** → **Services** → **IAM**.
   - Select the **IAM User**: `boopathy`.  
     **Important:** Always use an IAM user to generate security credentials. Using the root user is highly discouraged. 
   - Click the **Security credentials** tab.
   - Click **Create access key**.
   - Copy the **Access Key ID** and **Secret Access Key**.


2. **Configure AWS CLI:**
   Open the command line and configure AWS CLI using the credentials:
   ``` bash
   aws configure
   AWS Access Key ID [None]: 
   AWS Secret Access Key [None]: 
   Default region name [None]: us-east-1
   Default output format [None]: json
  

# Test if AWS CLI is working after configuring the above 

aws ec2 describe-vpcs


## Installing kubectl CLI

**IMPORTANT NOTE:**  
It is highly recommended to use the `kubectl` binaries provided by Amazon (Amazon EKS-vended `kubectl` binary). These binaries ensure compatibility with the specific version of your Amazon EKS cluster.  

Refer to the official documentation for detailed steps to download and install the appropriate `kubectl` binary:  
[Install kubectl for Amazon EKS](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)

---

## Installing eksctl CLI on Windows or Linux

For installing `eksctl` on Windows or Linux, follow the official documentation provided by Amazon:  
[Install eksctl](https://docs.aws.amazon.com/eks/latest/userguide/eksctl.html#installing-eksctl)

## Create EKS Cluster using eksctl
- It will take 15 to 20 minutes to create the Cluster Control Plane
```
# Create Cluster
eksctl create cluster --name=eksdemo1 \
                      --region=us-east-1 \
                      --zones=us-east-1a,us-east-1b \
                      --without-nodegroup 

# Get List of clusters
eksctl get cluster                  
``` 
## Step-2: Create & Associate IAM OIDC Provider for our EKS Cluster

- To enable and use AWS IAM roles for Kubernetes service accounts on our EKS cluster, we must create &  associate OIDC identity provider.
- To do so using `eksctl` we can use the  below command. 
- Use latest eksctl version 

```                  


# Replace with region & cluster name
eksctl utils associate-iam-oidc-provider \
    --region us-east-1 \
    --cluster eksdemo1 \
    --approve
```
## Step-03: Create EC2 Keypair
- Create a new EC2 Keypair with name as `kube-demo`
- This keypair we will use it when creating the EKS NodeGroup.
- This will help us to login to the EKS Worker Nodes using Terminal.

## Step-04: Create Node Group with additional Add-Ons in Public Subnets
- These add-ons will create the respective IAM policies for us automatically within our Node Group role.
 ```
# Create Public Node Group   
eksctl create nodegroup --cluster=eksdemo1 \
                        --region=us-east-1 \
                        --name=eksdemo1-ng-public1 \
                        --node-type=t3.medium \
                        --nodes=1 \
                        --nodes-min=1 \
                        --nodes-max=2 \
                        --node-volume-size=20 \
                        --ssh-access \
                        --ssh-public-key=job \
                        --managed \
                        --asg-access \
                        --external-dns-access \
                        --full-ecr-access \
                        --appmesh-access \
                        --alb-ingress-access 
```

## Step-05: Verify Cluster & Nodes

### Verify NodeGroup subnets to confirm EC2 Instances are in Public Subnet
- Verify the node group subnet to ensure it created in public subnets
  - Go to Services -> EKS -> eksdemo -> eksdemo1-ng1-public
  - Click on Associated subnet in **Details** tab
  - Click on **Route Table** Tab.
  - We should see that internet route via Internet Gateway (0.0.0.0/0 -> igw-xxxxxxxx)

### Verify Cluster, NodeGroup in EKS Management Console
- Go to Services -> Elastic Kubernetes Service -> eksdemo1

### export region
 export AWS_REGION=us-east-1
### List Worker Nodes
```
# List EKS clusters
eksctl get cluster



# List Nodes in current kubernetes cluster
kubectl get nodes -o wide
```
# Delete EKS Cluster & Node Groups

## Step-01: Delete Node Group
- We can delete a nodegroup separately using below `eksctl delete nodegroup`
```
# List EKS Clusters
eksctl get clusters

# Capture Node Group name
eksctl get nodegroup --cluster=eksdemo1

# Delete Node Group
eksctl delete nodegroup --cluster=eksdemo1 --name=eksdemo1-ng-public1
```

## Step-02: Delete Cluster  
- We can delete cluster using `eksctl delete cluster`
```
# Delete Cluster
eksctl delete cluster <clusterName>
eksctl delete cluster eksdemo1
```









