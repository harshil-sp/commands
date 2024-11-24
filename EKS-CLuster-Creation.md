---

# EKS Cluster and Node Group Setup with `eksctl`

This guide outlines the commands to create an **Amazon EKS Cluster**, configure IAM OIDC, and add a **Node Group** using the `eksctl` tool.

## 🛠️ Prerequisites

Ensure you have the following setup before proceeding:
- **AWS CLI** installed and configured with appropriate permissions.
- **eksctl** installed. You can download it [here](https://eksctl.io/).
- An SSH key pair for the specified region (`ap-south-1`) to allow SSH access to the nodes.

---

## 📋 Steps to Set Up

### 1. **Create the EKS Cluster Without a Node Group**

Run the following command to create a cluster named `cicd-cluster` in the `ap-south-1` region across two availability zones (`ap-south-1a` and `ap-south-1b`):

```bash
eksctl create cluster --name=cicd-cluster --region=ap-south-1 --zones=ap-south-1a,ap-south-1b --without-nodegroup
```

```bash
eksctl create cluster \
  --name=cicd-cluster \
  --region=ap-south-1 \
  --zones=ap-south-1a,ap-south-1b \
  --without-nodegroup
```

- **`--without-nodegroup`**: Ensures that no default node group is created. This gives you full control to define and create a custom node group.

---

### 2. **Associate IAM OIDC Provider**

Before creating the node group or deploying workloads, associate an IAM OIDC provider with your EKS cluster. This allows the cluster to manage AWS resources securely.

```bash
eksctl utils associate-iam-oidc-provider --region ap-south-1 --cluster cicd-cluster --approve
```

- **`--approve`**: Automatically approves the OIDC provider creation without prompting for confirmation.

---

### 3. **Create a Node Group**

Create a managed node group for the cluster using the following command:

```bash
eksctl create nodegroup --cluster=cicd-cluster --region=ap-south-1 --name=cicd-ng-public1 --node-type=t3.medium --nodes=2 --nodes-min=2 --nodes-max=4 --node-volume-size=20 --ssh-access --ssh-public-key=ap-south-1 --managed --asg-access --external-dns-access --full-ecr-access --appmesh-access --alb-ingress-access
```

```bash
eksctl create nodegroup \
  --cluster=cicd-cluster \
  --region=ap-south-1 \
  --name=cicd-ng-public1 \
  --node-type=t3.medium \
  --nodes=2 \
  --nodes-min=2 \
  --nodes-max=4 \
  --node-volume-size=20 \
  --ssh-access \
  --ssh-public-key=ap-south-1 \
  --managed \
  --asg-access \
  --external-dns-access \
  --full-ecr-access \
  --appmesh-access \
  --alb-ingress-access
```

#### 📝 Explanation of Parameters:

| Parameter                | Description                                                                                 |
|--------------------------|---------------------------------------------------------------------------------------------|
| `--cluster`             | Name of the EKS cluster to attach the node group to (`cicd-cluster`).                       |
| `--region`              | AWS region where the cluster and node group are created (`ap-south-1`).                     |
| `--name`                | Name of the node group (`cicd-ng-public1`).                                                 |
| `--node-type`           | EC2 instance type for the nodes (`t3.medium`).                                              |
| `--nodes`               | Desired number of nodes in the group (2).                                                   |
| `--nodes-min`           | Minimum number of nodes for auto-scaling (2).                                               |
| `--nodes-max`           | Maximum number of nodes for auto-scaling (4).                                               |
| `--node-volume-size`    | Size of the root volume attached to each node (20 GiB).                                      |
| `--ssh-access`          | Enables SSH access to the nodes.                                                            |
| `--ssh-public-key`      | Specifies the name of the SSH public key to use (`ap-south-1`).                             |
| `--managed`             | Creates a managed node group (recommended for EKS).                                         |
| `--asg-access`          | Grants permissions to the Auto Scaling Group associated with the nodes.                     |
| `--external-dns-access` | Enables integration with ExternalDNS.                                                       |
| `--full-ecr-access`     | Provides full access to Amazon Elastic Container Registry (ECR).                            |
| `--appmesh-access`      | Enables integration with AWS App Mesh.                                                      |
| `--alb-ingress-access`  | Grants permissions to manage Application Load Balancer (ALB) ingress controllers.           |

---

## ⚡ Verify the Setup

1. **Check Cluster Status**:
   ```bash
   eksctl get cluster --region=ap-south-1
   ```

2. **Verify Node Group**:
   ```bash
   eksctl get nodegroup --cluster=cicd-cluster --region=ap-south-1
   ```

3. **Access Nodes**:
   Use the SSH key (`ap-south-1`) to connect to a node for debugging or verification:
   ```bash
   ssh -i /path/to/ap-south-1.pem ec2-user@<node-public-ip>
   ```

---

## 🎉 Congratulations

Your EKS cluster is fully configured with IAM OIDC, and a managed node group is ready for your workloads!

--- 
