# Deploy an Application in Fargate cluster And Access From Browser

# step1: create eks fargate cluster as name thej-cluster in us-east-1 region  

# step2: create custome fargate profile for setup game-2048 namespace
- A fargate profile containe already default fargate profile we able to deploy our application into that also 
- To create custome fargate profile and deploy pod inside fargate profile 
```bash
eksctl create fargateprofile \
--cluster thej-cluster \
--region us-east-1 \
--name alb-sample-app \
--namespace game-2048
```
> From above command a new fargate profile got created in "thej-cluster" as in the custome namespace "game-2048" it will take minimum 3 to 5 min time 

## output
```bash
[ec2-user@ip-172-31-89-76 ~]$ eksctl create fargateprofile \
    --cluster thej-cluster-1 \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
2024-01-30 11:34:26 [ℹ]  creating Fargate profile "alb-sample-app" on EKS cluster "thej-cluster-1"
2024-01-30 11:38:44 [ℹ]  created Fargate profile "alb-sample-app" on EKS cluster "thej-cluster-1"
```

# step3: Deploy pod using yaml file 
   
```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```
> above yaml file contain namespace,  deployment, service and Ingress To setup Application inside pod 

## Output

```bash
[ec2-user@ip-172-31-89-76 ~]$kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balanc r-controller/v2.5.4/docs/examples/2048/2048_full.yamll
namespace/game-2048 created
deployment.apps/deployment-2048 created
service/service-2048 created
ingress.networking.k8s.io/ingress-2048 created
[ec2-user@ip-172-31-89-76 ~]$
```

> From above yaml file we deployed ingress controller also this ingress controller capable to access our pod inside cluster This Ingress controller works like Route The Traffic path Inside cluster 

- To check all pods in game-2048 namespace 
```bash	
kubectl get pods -n game-2048
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$ kubectl get pods -n game-2048
NAME                               READY   STATUS    RESTARTS   AGE
deployment-2048-75db5866dd-94gcv   1/1     Running   0          56s
deployment-2048-75db5866dd-k4r5m   1/1     Running   0          56s
deployment-2048-75db5866dd-p524j   1/1     Running   0          56s
deployment-2048-75db5866dd-s7ldq   1/1     Running   0          56s
deployment-2048-75db5866dd-zdgwx   1/1     Running   0          56s
[ec2-user@ip-172-31-89-76 ~]$
```

- To check service in game-2048 namespace 
```bash
kubectl get svc -n game-2048
```
## output
```bash    
[ec2-user@ip-172-31-89-76 ~]$kubectl get svc -n game-2048
NAME           TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
service-2048   NodePort   10.100.199.159   <none>        80:31906/TCP   85s
[ec2-user@ip-172-31-89-76 ~]$
```

- To check Ingress 
```bash
kubectl get ingress -n game-2048 
```
## Output

```bash
[ec2-user@ip-172-31-89-76 ~]$kubectl get ingress -n game-2048
NAME           CLASS   HOSTS   ADDRESS   PORTS   AGE
ingress-2048   alb     *                 80      2m3s
[ec2-user@ip-172-31-89-76 ~]$
```

- When we observer above output observer HOSTS (*) anyone can access but in Address ( ) no specification specified 
- To access from outside environment we have to add ALB controller add on pluggin 
- Ingress only works To access pod inside cluster only 
- To access pod from outside also here we need to add ALB Add on pluggin 
- To setup Alb add on we need to create IAM OIDC provider 

```bash	
eksctl utils associate-iam-oidc-provider --cluster thej-cluster-1 --approve
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$eksctl utils associate-iam-oidc-provider --cluster thej-cluster-1 --approve
2024-01-30 12:06:45 [ℹ]  will create IAM Open ID Connect provider for cluster "thej-cluster-1" in "us-east-1"
2024-01-30 12:06:45 [✔]  created IAM Open ID Connect provider for cluster "thej-cluster-1" in "us-east-1"
[ec2-user@ip-172-31-89-76 ~]$
```


# step4: setup ALB add On 

- To setup ALB add on first we need to create Role with some IAM policy 

1. Download IAM policy from github using curl command 
```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
```

## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controer/v2.5.4/docs/install/iam_policy.jsonson
    % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                    Dload  Upload   Total   Spent    Left  Speed
100  8386  100  8386    0     0   160k      0 --:--:-- --:--:-- --:--:--  163k
[ec2-user@ip-172-31-89-76 ~]$

[ec2-user@ip-172-31-89-76 ~]$ ls
iam_policy.json  
[ec2-user@ip-172-31-89-76 ~]$
```


- now create policy 
```bash
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

## Output 
```bash
[ec2-user@ip-172-31-89-76 ~]$ aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
{
    "Policy": {
        "PolicyName": "AWSLoadBalancerControllerIAMPolicy",
        "PolicyId": "ANPA2LW3XXCQVK4VDRTIT",
        "Arn": "arn:aws:iam::712351660193:policy/AWSLoadBalancerControllerIAMPolicy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2024-01-30T12:11:19+00:00",
        "UpdateDate": "2024-01-30T12:11:19+00:00"
    }
}
[ec2-user@ip-172-31-89-76 ~]$
```

> From above command a policy got created using "AWSLoadBalancerControllerIAMPolicy" name 

3. Create IAM Role

- Below is Syntax  to create iam service account 
```bash
eksctl create iamserviceaccount \
    --cluster=<your-cluster-name> \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve
```
> Change root id number and cluster name  
```bash
eksctl create iamserviceaccount \
    --cluster=thej-cluster-1 \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::712351660193:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve
```

## OutPut

```bash
[ec2-user@ip-172-31-89-76 ~]$ eksctl create iamserviceaccount \
    --cluster=thej-cluster-1 \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::712351660193:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve
2024-01-30 12:17:41 [ℹ]  1 iamserviceaccount (kube-system/aws-load-balancer-controller) was included (based on the include/exclude rules)
2024-01-30 12:17:41 [!]  serviceaccounts that exist in Kubernetes will be excluded, use --override-existing-serviceaccounts to override
2024-01-30 12:17:41 [ℹ]  1 task: {
    2 sequential sub-tasks: {
        create IAM role for serviceaccount "kube-system/aws-load-balancer-controller",
        create serviceaccount "kube-system/aws-load-balancer-controller",
    } }2024-01-30 12:17:41 [ℹ]  building iamserviceaccount stack "eksctl-thej-cluster-1-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2024-01-30 12:17:41 [ℹ]  deploying stack "eksctl-thej-cluster-1-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2024-01-30 12:17:41 [ℹ]  waiting for CloudFormation stack "eksctl-thej-cluster-1-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2024-01-30 12:18:12 [ℹ]  waiting for CloudFormation stack "eksctl-thej-cluster-1-addon-iamserviceaccount-kube-system-aws-load-balancer-controller"
2024-01-30 12:18:12 [ℹ]  created serviceaccount "kube-system/aws-load-balancer-controller"
[ec2-user@ip-172-31-89-76 ~]$
```


# step5: Deploy ALB controller

- Install Helm in the mahine 
- Add helm repo
```bash
helm repo add eks https://aws.github.io/eks-charts
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$ helm repo add eks https://aws.github.io/eks-charts
"eks" has been added to your repositories
[ec2-user@ip-172-31-89-76 ~]$
```
- Update the repo
```bash
	helm repo update eks
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$helm repo update eks
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "eks" chart repository
Update Complete. ⎈Happy Helming!⎈
[ec2-user@ip-172-31-89-76 ~]$
```

- Install

## Syntax
```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
    -n kube-system \
    --set clusterName=<your-cluster-name> \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set region=<region> \
    --set vpcId=<your-vpc-id>
```
> Add VPC id and region and cluster name 
```bash
helm install aws-load-balancer-controller eks/aws-load-balancer-controller \            
    -n kube-system \
    --set clusterName=thej-cluster-1 \
    --set serviceAccount.create=false \
    --set serviceAccount.name=aws-load-balancer-controller \
    --set region=us-east-1 \
    --set vpcId=vpc-027d5d0df04bd8b8d
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$ helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=thej-cluster-1 --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller --set region=us-east-1 --set vpcId=vpc-027d5d0df04bd8b8d
NAME: aws-load-balancer-controller
LAST DEPLOYED: Tue Jan 30 12:28:19 2024
NAMESPACE: kube-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
AWS Load Balancer controller installed!
[ec2-user@ip-172-31-89-76 ~]$
```
>> Note: To Unstall above ALB # helm uninstall aws-load-balancer-controller -n kube-system

- Verify that the deployments are running.
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```
```bash
[ec2-user@ip-172-31-89-76 ~]$ kubectl get deployment -n kube-system aws-load-balancer-controller
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   2/2     2            2           3m34s
[ec2-user@ip-172-31-89-76 ~]$
```
- Verify pods creation continuosly 
```bash
kubectl get pods -n kube-system -w 
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]$ kubectl get pods -n kube-system
NAME                                            READY   STATUS    RESTARTS   AGE
aws-load-balancer-controller-6f94f894dc-n8nmp   1/1     Running   0          6m31s
aws-load-balancer-controller-6f94f894dc-s95lv   1/1     Running   0          6m31s
coredns-5587ff98f-5x4ws                         1/1     Running   0          111m
coredns-5587ff98f-dfn87                         1/1     Running   0          111m
[ec2-user@ip-172-31-89-76 ~]$
```
- To check deployments of aws-load-balancer-controller 
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```
## Output
```bash
[ec2-user@ip-172-31-89-76 ~]kubectl get deployment -n kube-system aws-load-balancer-controller
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
aws-load-balancer-controller   2/2     2            2           8m6s
[ec2-user@ip-172-31-89-76 ~]$
```

- Verify ALB created or not from console in the Load balancer section 


- check ingress 
```bash
[ec2-user@ip-172-31-89-76 ~]$ kubectl get ingress -n game-2048
NAME           CLASS   HOSTS   ADDRESS                                                                  PORTS   AGE
ingress-2048   alb     *       k8s-game2048-ingress2-83f5e653cf-295140272.us-east-1.elb.amazonaws.com   80      48m
[ec2-user@ip-172-31-89-76 ~]$
```
- Wait Atleast 3 to 5 min for loadbalancer active 

# step6: To Delete Cluster 
1. Delete pods in a specific file what ever it containes
```bash
kubectl delete -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

- To delete only pods 
```bash
kubectl delete pods -l app=2048-game
```

2. Delete ALB
- Below This command will remove the Helm release named aws-load-balancer-controller from the kube-system namespace
```bash
helm uninstall aws-load-balancer-controller -n kube-system
```
- After deleting delete remaing resources which is required to setup ALB also like IAM Policy and Role 
```bash
aws iam delete-policy --policy-arn arn:aws:iam::712351660193:policy/AWSLoadBalancerControllerIAMPolicy
```

3. Delete fargate profiles
```bash
eksctl delete fargateprofile --cluster thej-cluster --region us-east-1 --name alb-sample-app
```

4. delete  cluster
```bash
	eksctl delete cluster --name thej-cluster-1 --region us-east-1
```
## Outout 
```bash
[ec2-user@ip-172-31-89-76 ~]$ eksctl delete cluster --name thej-cluster-1 --region us-east-1
2024-01-30 12:58:00 [ℹ]  deleting EKS cluster "thej-cluster-1"
2024-01-30 12:58:00 [ℹ]  deleting Fargate profile "alb-sample-app"
2024-01-30 13:02:16 [ℹ]  deleted Fargate profile "alb-sample-app"
2024-01-30 13:02:16 [ℹ]  deleting Fargate profile "fp-default"
2024-01-30 13:06:32 [ℹ]  deleted Fargate profile "fp-default"
2024-01-30 13:06:32 [ℹ]  deleted 2 Fargate profile(s)
2024-01-30 13:06:32 [ℹ]  cleaning up AWS load balancers created by Kubernetes objects of Kind Service or Ingress
Error: deadline surpassed waiting for AWS load balancers to be deleted: k8s-game2048-ingress2-83f5e653cf
[ec2-user@ip-172-31-89-76 ~]$
```

