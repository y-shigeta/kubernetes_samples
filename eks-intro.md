Kubernets sample yaml
===

# Overview

This is introduction for eks

## Install
- install eks
```
curl -L "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl -version
```

- install tools
```
brew install bash-completion
echo "[ -f /usr/local/etc/bash_completion ] && . /usr/local/etc/bash_completion" >> ~/.bash_profile

sudo curl -L -o /usr/local/etc/bash_completion.d/docker https://raw.githubusercontent.com/docker/cli/master/contrib/completion/bash/docker
sudo curl -L -o /usr/local/etc/bash_completion.d/docker-compose https://raw.githubusercontent.com/docker/compose/1.29.2/contrib/completion/bash/docker-compose

kubectl completion bash > kubectl_completion
sudo mv kubectl_completion /usr/local/etc/bash_completion.d/kubectl
eksctl completion bash > eksctl_completion
sudo mv eksctl_completion /usr/local/etc/bash_completion.d/eksctl

cat <<"EOT" >> ${HOME}/.bash_profile
alias k="kubectl"
complete -o default -F __start_kubectl k
EOT

git clone https://github.com/jonmosco/kube-ps1.git ~/.kube-ps1
cat <<"EOT" >> ~/.bash_profile
source ~/.kube-ps1/kube-ps1.sh
function get_cluster_short() {
  echo "$1" | cut -d . -f1
}
KUBE_PS1_CLUSTER_FUNCTION=get_cluster_short
KUBE_PS1_SUFFIX=') '
PS1='$(kube_ps1)'$PS1
EOT


git clone https://github.com/ahmetb/kubectx.git ~/.kubectx
sudo ln -sf ~/.kubectx/completion/kubens.bash /usr/local/etc/bash_completion.d/kubens
sudo ln -sf ~/.kubectx/completion/kubectx.bash /usr/local/etc/bash_completion.d/kubectx
cat <<"EOT" >> ~/.bash_profile
export PATH=~/.kubectx:$PATH
EOT

brew install stern

exec $SHELL -l
```

- create eks cluster
```
AWS_REGION=$(aws configure get default.region)
eksctl create cluster \
  --name=ekshandson \
  --version 1.23 \
  --nodes=3 --managed \
  --region ${AWS_REGION} --zones ${AWS_REGION}a,${AWS_REGION}c

- create eks cluster within the exiting VPC
eksctl create cluster \
  --name=example-dev \
  --version 1.23 \
  --spot \
  --nodes=3 --managed \
  --region ${AWS_REGION} \
  --node-private-networking \
  --vpc-private-subnets "subnet-04717f3fdb642fe4f","subnet-0df7a1a0ffa2a6cec"
```
## Usage for tools

- list kubectl context
kubectx
- change context
kubectx gke_white-artwork-286219_us-central1_autopilot-cluster-1
kubectx arn:aws:eks:us-east-1:139082967877:cluster/ekshandson


## Setup Sample App by reference
- Reference
https://catalog.us-east-1.prod.workshops.aws/workshops/f5abb693-2d87-43b5-a439-77454f28e2e7/ja-JP/020-create-cluster/30-configure-useful-tools

- create dynamodb
```
aws dynamodb create-table --table-name 'messages' \
  --attribute-definitions '[{"AttributeName":"uuid","AttributeType": "S"}]' \
  --key-schema '[{"AttributeName":"uuid","KeyType": "HASH"}]' \
  --provisioned-throughput '{"ReadCapacityUnits": 1,"WriteCapacityUnits": 1}'
```


- create ecr
aws ecr create-repository --repository-name frontend
aws ecr create-repository --repository-name backend
frontend_repo=$(aws ecr describe-repositories --repository-names frontend --query 'repositories[0].repositoryUri' --output text)
backend_repo=$(aws ecr describe-repositories --repository-names backend --query 'repositories[0].repositoryUri' --output text)

- login ECR
```
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
AWS_REGION=$(aws configure get default.region)
aws ecr get-login-password | docker login --username AWS --password-stdin https://${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
```

- check docker images
```
cd sample-app
docker-compose build
docker images
docker tag frontend:latest ${frontend_repo}:latest
docker tag backend:latest ${backend_repo}:latest
```
- docker push
```
docker push ${frontend_repo}:latest
docker push ${backend_repo}:latest
```

cd k8s
kubectl create namespace frontend
kubens frontend
kubectl apply -f frontend-deployment.yaml -n frontend
kubectl get deployment -n frontend
kubectl apply -f frontend-service-lb.yaml -n frontend
kubectl get service -n frontend
kubectl create namespace backend
kubens backend
kubectl apply -f backend-deployment.yaml -n backend
kubectl get pod -n backend
kubectl apply -f backend-service.yaml -n backend
kubectl get service -n backend


- check container log
stern frontend -n frontend
stern backend --since 3m -n backendく

- enable IRSA to allow pods to access 
CLUSTER=example-dev
eksctl utils associate-iam-oidc-provider \
    --cluster ${CLUSTER} \
    --approve

- Create IAM policy for dynamodb access
aws iam create-policy \
    --policy-name dynamodb-messages-fullaccess \
    --policy-document file://dynamodb-messages-fullaccess-policy.json

- attache IAM poclicy to IRSA
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
eksctl create iamserviceaccount \
    --name dynamodb-messages-fullaccess \
    --namespace backend \
    --cluster ${CLUSTER} \
    --attach-policy-arn arn:aws:iam::${AWS_ACCOUNT_ID}:policy/dynamodb-messages-fullaccess \
    --override-existing-serviceaccounts \
    --approve
kubectl get serviceaccount -n backend
kubectl describe serviceaccount dynamodb-messages-fullaccess -n backend

kubectl apply -f backend-deployment.yaml -n backend

## Cleanup
eksctl delete iamserviceaccount --cluster ekshandson --namespace backend --name dynamodb-messages-fullaccess
eksctl delete cluster --name ekshandson
kubectl delete namespace frontend backend
aws dynamodb delete-table --table-name 'messages'
AWS_ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
aws iam delete-policy --policy-arn arn:aws:iam::${AWS_ACCOUNT_ID}:policy/dynamodb-messages-fullaccess
aws ecr delete-repository --repository-name frontend --force
aws ecr delete-repository --repository-name backend --force

## More advanced
https://www.eksworkshop.com/




## Licence

## Author
[y-shigeta](https://github.com/y-shigeta)