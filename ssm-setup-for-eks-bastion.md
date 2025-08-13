Always show details

Copy
# Let's create the README file with the provided content.

readme_content = """# Setting Up Amazon SSM Agent on EKS Cluster & Bastion

This guide walks you through configuring AWS Systems Manager (SSM) Agent to work with an **EKS cluster** and a **Bastion EC2 instance**, along with IAM role policies and connectivity setup.

---

## 1️⃣ Create an IAM Role with Required Policies

Attach the following AWS managed and custom inline policies to the IAM role.

### AWS Managed Policies
- **AmazonSSMFullAccess**
- **AmazonSSMManagedInstanceCore**

---

### Customer Inline Policies

#### 📄 Asseto-prod-eks-access-policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Action": [
                "eks:DescribeCluster",
                "eks:ListClusters",
                "eks:ListNodegroups",
                "eks:DescribeNodegroup"
            ],
            "Effect": "Allow",
            "Resource": "arn:aws:eks:ap-southeast-5:707728974508:cluster/asseto-prod-eks-cluster"
        }
    ]
}
📄 eks-full-access-policy
json
Always show details

Copy
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "eks:DescribeCluster",
                "eks:ListClusters",
                "eks:ListNodegroups",
                "eks:DescribeNodegroup",
                "eks:ListFargateProfiles",
                "eks:DescribeFargateProfile",
                "eks:ListUpdates",
                "eks:DescribeUpdate",
                "eks:ListAddons",
                "eks:DescribeAddon"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "ec2:DescribeInstances",
                "ec2:DescribeSubnets",
                "ec2:DescribeSecurityGroups"
            ],
            "Resource": "*"
        }
    ]
}
2️⃣ Attach the Role to Bastion EC2 Instance
Go to EC2 Console → Select your Bastion EC2 instance → Actions → Security → Modify IAM role → Attach the role created above.

3️⃣ Verify Access
SSH into the Bastion EC2 instance.

Ensure the IAM role is attached:

bash
Always show details

Copy
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
Restart the instance if necessary:

bash
Always show details

Copy
sudo reboot
4️⃣ Connect to the EKS Cluster from Bastion
Follow the official AWS documentation for EKS access:

📄 References

Update kubeconfig for EKS

Install kubectl

EKS Overview

Steps
Configure AWS CLI on Bastion:

bash
Always show details

Copy
aws configure
(Provide the IAM user Access Key and Secret Key.)

Add IAM User Access in EKS Console:

Navigate to EKS → Access → IAM access entries.

Create access entry:

Select your EKS cluster role.

Type = Standard

Add Access Policies:

AmazonEKSAdminPolicy

AmazonEKSClusterAdminPolicy

Provide the IAM user ARN.

Update kubeconfig on Bastion:

bash
Always show details

Copy
aws eks --region <region> update-kubeconfig --name <cluster_name>
Verify Cluster Access:

bash
Always show details

Copy
kubectl get nodes
✅ Your Bastion EC2 instance should now be able to manage the EKS cluster via SSM.
"""

