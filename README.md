# game-2048appwithIngressController
Deployment of an app of game 2048 in EKS Fargate and give external access through Ingress Controller having ingress resource.
# Rough note - pod, deploy, serv, ingress, ingress controller - created load balancer (using helm chart) IAM + attached with service account.
# pre-requisites
kubectl
helm
aws cli (aws configure)
eksctl

# Notes for steps :
## Create eks custer
A. eksctl create cluster --name demo-cluster --region us-east-1 --fargate
What is happening (uses helm) - this eksctl is creating cluster service role, networking (public and private subnet)
In private subnet we will keep our application.

B. aws eks update-kubeconfig --name demo-cluster --region us-east-1

C. Create fargate profile - we have to create fargate profile for deploying app. Like affinity of bare metal kubernetes cluster.

D. Deploy the yaml

E. Address for ingress is not created because ingresscontroller is not created
% kubectl get svc -n game-2048 
NAME           TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
service-2048   NodePort   10.100.6.128   <none>        80:31596/TCP   57s

% kubectl get ingress -n game-2048

F. IAM integration is required through IAM OIDC provider to talk to AWS service.
eksctl utils associate-iam-oidc-provider --cluster demo-cluster --approve --region us-east-1

G. ALB controller add on
Create policy for AWS LB controller IAM - AWSLoadBalancerControllerIAMPolicy
Create rolle - Attach IAM policy above with the role - having specific permission.

H. Create ALB controller using helm chart
https://github.com/helm/helm/issues/10975#issuecomment-1130758363
install helm 3.8.2

Load balancer controller created the load balancer (can be seen in EC2 instance) because having ingress resource.
