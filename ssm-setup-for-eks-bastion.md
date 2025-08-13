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
