2048 game:

connect to aws using aws configure in aws cli

create cluster in EKS.
create using EKSCTL.

eksctl create cluster --name demo-cluster --region us-east-1 --fargate

(the last parameter, you can change if ypu want to use ec2, here we can proceed with fargate).

Create cluster:
eksctl create cluster --name demo-cluster-project --region ca-central-1 --fargate

OPENID Connect Provider(OIDC):
you can create an identity provider and attach it to all users
For eg: popup shows to login with FB, login with LINKEDIN (everything are OIDC).


Cluster name: demo-cluster-project 

Update cluster:
aws eks update-kubeconfig --name demo-cluster-project --region ca-central-1

Deployment:

Deploy pod:
eksctl create fargateprofile \
    --cluster demo-cluster-project \
    --region ca-central-1 \
    --name alb-sample-app \
    --namespace game-2048
    x


kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
output: namespace/game-2048 created
deployment.apps/deployment-2048 created
service/service-2048 created
ingress.networking.k8s.io/ingress-2048 created

kubectl get pods -n game-2048

kubectl get svc -n game-2048

kubectl get ingress -n game-2048


eksctl utils associate-iam-oidc-provider --cluster game-cluster-demo --approve

curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json


Create IAM Policy:
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

Output: {
    "Policy": {
        "PolicyName": "AWSLoadBalancerControllerIAMPolicy",
        "PolicyId": "ANPA4SQVHGWR4JUL4Q7KQ",
        "Arn": "arn:aws:iam::864406680995:policy/AWSLoadBalancerControllerIAMPolicy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2023-11-27T21:26:35+00:00",
        "UpdateDate": "2023-11-27T21:26:35+00:00"
    }
}



Create IAM Role:
eksctl create iamserviceaccount \
  --cluster=demo-cluster-project \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::864406680995:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve

output:
2023-11-27 16:28:44 [ℹ]  1 iamserviceaccount (kube-system/aws-load-balancer-controller) was included (based on the include/exclude rules)
2023-11-27 16:28:44 [!]  serviceaccounts that exist in Kubernetes will be excluded, use --override-existing-serviceaccounts to override
2023-11-27 16:28:44 [ℹ]  1 task: {
    2 sequential sub-tasks: {
        create IAM role for serviceaccount "kube-system/aws-load-balancer-controller",
        create serviceaccount "kube-system/aws-load-balancer-controller",
    } }2023-11-27 16:28:44 [ℹ]  building iamserviceaccount stack "eksctl-game-cluster-demo-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2023-11-27 16:28:45 [ℹ]  deploying stack "eksctl-game-cluster-demo-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2023-11-27 16:28:45 [ℹ]  waiting for CloudFormation stack "eksctl-game-cluster-demo-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2023-11-27 16:29:15 [ℹ]  waiting for CloudFormation stack "eksctl-game-cluster-demo-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2023-11-27 16:29:15 [ℹ]  created serviceaccount "kube-system/aws-load-balancer-controller"




Creation of ALB controller: ingress controller: 
helm repo add eks https://aws.github.io/eks-charts

Helm repo update eks

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=demo-cluster-project \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=ca-central-1 \
  --set vpcId=vpc-041bd0703a8e52a24
output:
NAME: aws-load-balancer-controller
LAST DEPLOYED: Fri Feb  9 12:58:16 2024
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
AWS Load Balancer controller installed!


Verify that deployments are running:
kubectl get deployment -n kube-system aws-load-balancer-controller

kubectl get pods -n kube-system

To debug:
Kubectl edit deploy/aws-load-balancer-controller -n cube-system

To delete:

Helm-delete aws-load-balancer -n kube-system

kubectl get deploy -n kube-system



eksctl delete cluster --name game-cluster-demo --region us-east-1
