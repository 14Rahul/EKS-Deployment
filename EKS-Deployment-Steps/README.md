# EKS Deployment Steps

## Prerequisite

- Install AWS CLI
- Install eksctl
- Install Kubectl
- Install Helm
- Privide Required Roles to Server to create cluster
- Install Docker to Build Docker Images

## Create Cluster

Set Appropriate Region Using below command
- aws configure

To Create EKS using eksctl use below command
- eksctl create cluster -f helloworldclustercreate.yml

To Upgrade EKS using eksctl use below command
- eksctl upgrade cluster -f helloworldclustercreate.yml --approve

To Delete EKS using eksctl use below command
- eksctl delete cluster -f helloworldclustercreate.yml

Configure kubectl to connect to EKS Cluster on AWS
- aws eks update-kubeconfig --name <Cluster Name>

## Enable Cluster Nodes Autoscaler

Chanege Cluster Name in Cluster Autoscaler Policy JSON file and create policy
- aws iam create-policy --policy-name ClusterAutoscalerPolicy --policy-document file://ClusterAutoscalerPolicy.json

Set OIDC Provider for CLuster
- eksctl utils associate-iam-oidc-provider --cluster <Cluster> --approve

Create Service Account for Cluster Autoscaler and attach policy 
- eksctl create iamserviceaccount --cluster=<Cluster>   --namespace=kube-system --name=cluster-autoscaler --attach-policy-arn=arn:aws:iam::<Account ID>:policy/ClusterAutoscalerPolicy --override-existing-serviceaccounts --approve

Add below Tags to Cluster Autiscaling Group for Auto Descoveru of Autoscaling Group
- k8s.io/cluster-autoscaler/enabled=true
- k8s.io/cluster-autoscaler/<Cluster>=owned

Update Cluster Version and Cluster Name in YML file and Deploy the Cluster Autoscaler To Custer
- kubectl apply -f cluster-autoscaler-autodiscover.yaml
- kubectl get deploy/cluster-autoscaler -n kube-system
- kubectl get pods -n kube-system -l=app=cluster-autoscaler
- kubectl logs cluster-autoscaler-<pod id> -n kube-system

## Deploy Application on EKS Cluster

Deploy Application on EKS cluster
- kubectl apply -f helloworlddeployment.yml

## Enable Horizontal Pod Autoscaler

Deploy Metrics Server
- kubectl apply -f components.yml
- kubectl get deployment metrics-server -n kube-system

Deploy Horizontal Pod Autoscaler
- kubectl apply -f horizontalpodautoscaler.yml
- kubectl get hpa

## Deploy Ingress ALB for EKS

Create IAM Policy for Ingress ALB Controller
- aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json

Create Service Account and attach policy AWSLoadBalancerControllerIAMPolicy
- eksctl create iamserviceaccount --cluster=<Cluster> --namespace=kube-system     --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole     --attach-policy-arn="arn:aws:iam::<Account ID>:policy/AWSLoadBalancerControllerIAMPolicy"     --override-existing-serviceaccounts --approve

Update Helm Repos
- helm repo add eks https://aws.github.io/eks-charts
- helm repo update eks

Attach Below Tags to Subnets in Which ALB Will be created
- kubernetes.io/role/elb=1

Deploy Ingress Load Balancer Controller
- helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=<Cluster Name> --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller

Deploy ALB for Your Application
- kubectl apply -f ingress.yml

Use below Command for Troubleshooting issue
- kubectl get pods -n kube-system
- kubectl logs aws-load-balancer-controller-<Pod Id> -n kube-system

# The Application Deployed on this EKS Cluster is Highly Available and Scalable. 
