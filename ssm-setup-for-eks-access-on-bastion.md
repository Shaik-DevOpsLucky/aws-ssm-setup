# Setting Up Amazon SSM Agent on EKS Cluster & Bastion

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
```

---

#### 📄 eks-full-access-policy
```json
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
```

---

## 2️⃣ Attach the Role to Bastion EC2 Instance

- Go to **EC2 Console** → **Select your Bastion EC2 instance** → **Actions → Security → Modify IAM role** → Attach the role created above.

---

## 3️⃣ Verify Access

- SSH into the Bastion EC2 instance.
- Ensure the IAM role is attached:
```bash
curl http://169.254.169.254/latest/meta-data/iam/security-credentials/
```
- Restart the instance if necessary:
```bash
sudo reboot
```

---

## 4️⃣ Connect to the EKS Cluster from Bastion

Follow the official AWS documentation for EKS access:

📄 **References**
- [Update kubeconfig for EKS](https://docs.aws.amazon.com/cli/latest/reference/eks/update-kubeconfig.html)  
- [Install kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)  
- [EKS Overview](https://docs.aws.amazon.com/eks/latest/userguide/what-is-eks.html)  

---

### Steps

1. **Configure AWS CLI** on Bastion:
```bash
aws configure
```
(Provide the IAM user **Access Key** and **Secret Key**.)

2. **Add IAM User Access in EKS Console**:
    - Navigate to **EKS → Access → IAM access entries**.
    - **Create access entry**:
        - Select your EKS cluster role.
        - Type = **Standard**
        - Add Access Policies:
            - `AmazonEKSAdminPolicy`
            - `AmazonEKSClusterAdminPolicy`
    - Provide the **IAM user ARN**.

3. **Update kubeconfig** on Bastion:
```bash
aws eks --region <region> update-kubeconfig --name <cluster_name>
```

4. **Verify Cluster Access**:
```bash
kubectl get nodes
```

---

✅ **Your Bastion EC2 instance should now be able to manage the EKS cluster via SSM.**
