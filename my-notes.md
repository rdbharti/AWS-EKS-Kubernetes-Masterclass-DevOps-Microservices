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
#### Template
eksctl utils associate-iam-oidc-provider --region us-east-1 --cluster eksdemo1 --approve
```
 ## 3. Create EC2 KeyPair

- create a new EC2 Keypair with name as ```kube-demo```
- This keypair we will use it when creating the EKS NodeGroup.
- This will help us to login to the EKS Worker Nodes using Terminal.