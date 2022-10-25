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

=============================================
    Create EKS Cluster using eksctl 
============================================

[//]: <> (Create cluster without nodegroup)

```
eksctl create cluster --name=eksctldemo1 --region=us-east-1 --zones=us-east-1a,us-east-1b --without-nodegroup

```

[//]: <> (Get List of Clusters)
```
eksctl get clusters
```
