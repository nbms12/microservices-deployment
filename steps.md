Project AIM: Microservices CI/CD deployment ( 11 Microservices ) inside EKS clusters 

1. ec2 instances 

![image](https://github.com/user-attachments/assets/7198343d-841d-48ca-b981-6b7091b2dfc9)

2.  create iam policies for server written in setup-infra.md

3.  install awsci, eksctl , kubectl

   
4.  create eks cluter

```

eksctl create cluster --name=EKS-1 \
                      --region=ap-south-1 \
                      --zones=ap-south-1a,ap-south-1b \
                      --without-nodegroup

eksctl utils associate-iam-oidc-provider \
    --region ap-south-1 \
    --cluster EKS-1 \
    --approve

eksctl create nodegroup --cluster=EKS-1 \
                       --region=ap-south-1 \
                       --name=node2 \
                       --node-type=t3.medium \
                       --nodes=3 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=DevOps \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access

```
5.  install jenkins , docker

   
7.  Create Service Account, Role & Assign that role, And create a secret for Service Account and geenrate a Token

create namespace webapps 

```
kubectl create namespace -n webapps

```
Creating Service Account
```
apiVersion: v1
kind: ServiceAccount
metadata:
  name: jenkins
  namespace: webapps
```

```
kubectl apply -f svc.yml
```

Create Role
```
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: webapps
rules:
  - apiGroups:
        - ""
        - apps
        - autoscaling
        - batch
        - extensions
        - policy
        - rbac.authorization.k8s.io
    resources:
      - pods
      - componentstatuses
      - configmaps
      - daemonsets
      - deployments
      - events
      - endpoints
      - horizontalpodautoscalers
      - ingress
      - jobs
      - limitranges
      - namespaces
      - nodes
      - pods
      - persistentvolumes
      - persistentvolumeclaims
      - resourcequotas
      - replicasets
      - replicationcontrollers
      - serviceaccounts
      - services
    verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]

```
```
kubectl apply-f myrole.yml
```
bind tis role to serviceaccount created 

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: webapps 
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: app-role 
subjects:
- namespace: webapps 
  kind: ServiceAccount
  name: jenkins

```
```
kubectl apply -f bindrole.yml
```

create a secret for same serviceaccount ( jenkins ) 
```
apiVersion: v1
kind: Secret
type: kubernetes.io/service-account-token
metadata:
  name: mysecretname
  annotations:
    kubernetes.io/service-account.name: jenkins

```
```
kubrctl apply -f mysec.yml
```

8.  set permissions to docker socket to build images use : sudo chmod 666  /var/run/docker.sock

9.  create classic token in github main settings ( use tis as cred in jenkin , type secret text ) 

10.  create a webhook inside project repo ( add :  http://15.206.160.5:8080/multibranch-webhook-trigger/invoke?token=micro-tokens ) need to replace wit jenkins url and classic token name

11.  create a kubernetes token and  store into jenkins cred ( k8-token ) 

```
 kubectl describe secret mysecretname -n webapps
```

12. install tis pipeline and configure

![image](https://github.com/user-attachments/assets/3e4473b9-c114-464e-b0c7-06ebc5b8625b)

13.microservices  pipeline view using multibranch strategy 

![image](https://github.com/user-attachments/assets/e7a48a9a-806b-4174-a4e3-affe12e05717)



final frontend - app

![image](https://github.com/user-attachments/assets/de7d4d43-7496-4372-a878-d6f4bc466797)


