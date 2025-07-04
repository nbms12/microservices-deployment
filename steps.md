aim: microservices deployment ( 11 services ) inside EKS clusters wit 3 nodes 

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

8.  set permissions to docker socket to build images use : sudo chmod 666  /var/run/docker.sock

9.  

microservices 

![image](https://github.com/user-attachments/assets/e7a48a9a-806b-4174-a4e3-affe12e05717)



frontend - app

![image](https://github.com/user-attachments/assets/de7d4d43-7496-4372-a878-d6f4bc466797)


