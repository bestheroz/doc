# Amazon EKS 시작하기

#### Amazon EKS 요구 사항을 충족하는 퍼블릭 및 프라이빗 서브넷이 있는 Amazon VPC PC를 생성

```sh
PROJECT_NAME=base

eksctl create cluster --name $PROJECT_NAME --region ap-northeast-2 --zones ap-northeast-2a,ap-northeast-2c --fargate --managed --nodegroup-name default-node-group --spot -t t3a.small -N 2 -m 2 -M 2 --node-volume-size 20

aws eks update-kubeconfig --name $PROJECT_NAME

kubectl create namespace sandbox
kubectl create namespace qa
kubectl create namespace prod

eksctl create fargateprofile \
    --name fp-qa \
    --namespace qa \
    --cluster $PROJECT_NAME
    
eksctl create fargateprofile \
    --name fp-prod \
    --namespace prod \
    --cluster $PROJECT_NAME
    
```

## argoCD

```sh
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

argocd login demo-argocd.example.io
argocd account update-password
```



#### 타 계정에 eks 권한주기

클러스터 소유자가 권한을 직접 넣어줘야함

```sh
kubectl edit configmap aws-auth -n kube-system
```

data 하위에 user 넣기

```yaml
  mapUsers: |
    - userarn: arn:aws:iam::130382108370:user/testuser
      username: testuser
      groups:
        - system:masters
```



#### 환경 변경

```sh
PROJECT_NAME=base
aws eks update-kubeconfig --name $PROJECT_NAME

PROJECT_NAME=woojoo-star
aws eks update-kubeconfig --name $PROJECT_NAME
```

#### 삭제되지 않는 ns 자원 처리

```sh
kubectl api-resources --verbs=list --namespaced -o name \
  | xargs -n 1 kubectl get --show-kind --ignore-not-found -n argocd

kubectl edit ingress.networking.k8s.io/argocd-ingress -n argocd
```

