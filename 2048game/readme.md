2048 game is creates using AWS EKS

comparing clusterip, nodeport and load balancer mode ... Load balancer mode is best suitable to deploy this project

But Load balancer has cost issues, the cost is ultimately high if we use load balancer mode. 

the alternate option is to use ingress in kubernetes. 

master node -> api server
worker nide -> pod.yml

ingress in created around the pod using helm.

thus in this project, we use ingress controller option.

say if you want aws alb:
download helm and deploy ingress controller. this ingress controller watch for the ingress and create ALB.

using ingress class you can define who can watch for the pods.

User can access the pods through the ALB.