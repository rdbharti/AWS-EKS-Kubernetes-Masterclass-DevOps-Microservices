# AWS EKS - Core Objects 

## EKS CLuster
- EKS Ccontrol Plane
- Worker Node & Node Group
- Fargate Profiles(Serverless)
- VPC

## How does EKS work ?

1. Provision an EKS cluster
2. Deploy worker nodes
3. Connect to EKS
4. Run Kubernetes apps

##  1. Create EKS Cluster using eksctl 

[//]: <> (Create cluster without nodegroup)

```
eksctl create cluster --name=eksctldemo1 --region=us-east-1 --zones=us-east-1a,us-east-1b --without-nodegroup

```

[//]: <> (Get List of Clusters)
```
eksctl get clusters
```

 ##   2. Create & Associate IAM OIDC provider for our EKS Cluster

- To enable and use AWS IAM roles for Kubernets Service account on our EKS cluster, we must create and associate OIDC identity provider
- To do so using ``` eksctl ``` we can use the below command

```
#### Template
eksctl utils associate-iam-oidc-provider --region <region-code> --cluster <cluster-name> --approve
```

```
Code
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster eksdemo1 --approve
```
 ## 3. Create EC2 KeyPair

- create a new EC2 Keypair with name as ```kube-demo```
- This keypair we will use it when creating the EKS NodeGroup.
- This will help us to login to the EKS Worker Nodes using Terminal.

## 4. Create Node Group with ADD-ONs in public subnets
- Thses add-ons will create the respective IAM policies for us automatically within our NodeGroup role

```markdown

##### create Public Node Group 
eksctl create nodegroup --cluster=eksdemo1 \
                        --region=us-east-1 \
                        --name=eks-demo-ng-public1 \
                        --node-type=t2.micro \
                        --nodes=2 \
                        --nodes-min=2 \
                        --nodes-max=4 \
                        --node-volume-size=20 \
                        --shh-access \
                        --ssh-public-key=kube-demo \
                        --managed \
                        --asg-access \
                        --external-dns-access \
                        --full-ecr-access \
                        --appmesh-access \
                        --alb-ingress-access

```

## 5. Verify Cluster and Nodes

### Verify NodeGroup subnets to confirm EC2 instances are in Public Subnet
- Verify the node group subnet to ensure its created in public subnets
    - Go to Services -> EKS -> eksdemo1 -> eks-demo-ng-public1
    - click on Associate subnet in Details Tab
    - Click on Route Table Tab
    - We should see the internet route via Internet Gateway (0.0.0.0/0 -> igw-xxxxxx)

### Verify Cluster, NodeGroup,in EKS MManagement Console
- Go to Services -> Elastic Kubernetes Service -> eksdemo1

### List Worker Nodes

```markdown
# List EKD Clusters
eksctl get cluster

# List NodeGroups in a cluster
eksctl get nodegroup cluster=<cluster-name>

# List Nodes in current kubernetes cluster 
kubectl get nodes -o wide

# Our kubectl contetxt should be automatically changed to new cluster
kubectl config view --minify
```

### Verify Worer node IAM Role and list Policies
- Go to Services -> EC2 -> Worker Nodes
- Click on IAM Role associated to ec3 worker nodes

### Verify Security Group Associated to worker Nodes
- EC2 -> Worker Nodes
- Click on Security Group associated with ec2 instance which contains ```remote``` in the name

### Varify Cloudformation Stacks
- Verify Control Plane Stack Events
- Verify NodeGroup Stack and Events

### Login to Worker node using Key-Pair 
- login to worker node