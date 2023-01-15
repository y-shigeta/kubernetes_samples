# k8s command

kubectl cluster-info 
kubectl get node

kubectl get services
kubectl get pods --all-namespaces
kubectl get pods --all-namespaces -o wide  	#list IP address and node info
kubectl config view 	 # see config
kubectl config get-contexts 	 # see context

kubectl create -f [manifest yml] 
kubectl delete -f [manifest yml] 
kubectl delete pods ningx
kubectl apply -f [manifest yml] 
kubectl replace -f [manifest yml] 
kubectl scale —replicas=5 deployment/web-deploy	#scale-out

kubectl exec -it "web-deploy-654cd6d4d8-dbckq" -- /bin/bash

kubectl run hello-world --image=hello-world -it --restart=Never
kubectl get all
kubectl delete po
kubectl get deploy

- release, pause, resume, rollback
kubectl apply -f [manifest yml] 
kubectl get deploy
kubectl rollout pause deploy [deployment name]
kubectl rollout resume deploy [deployment name]
kubectl rollout history deploy [deployment name]
kubectl rollout undo deploy [deployment name] --to-revision 1

# login GKE
gcloud auth login
gcloud config set project sincere-mission-330023
gcloud container clusters get-credentials mycluster01 --region us-central1 --project sincere-mission-330023
gcloud container clusters get-credentials autopilot-cluster-1 --region us-central1 --project white-artwork-286219

# Docker認証
gcloud login auth		#login gcp
gcloud config set project white-artwork-286219
gcloud config list		# check config
gcloud auth configure-docker
docker tag httpd:latest gcr.io/white-artwork-286219/test-httpd/httpd:latest	#source imageとRepositoryを紐付け
docker push gcr.io/white-artwork-286219/test-httpd/httpd:latest			＃ImageをGCRにPush

gcloud artifacts repositories add-iam-policy-binding docker --location=us-central1 --member='shigetastartsawsuser01@gmail.com' --role='roles/artifactregistry.writer'

# 以下の内容でDockerfileを作成
cat   <<EOF > Dockerfile
FROM ubuntu
RUN apt-get install -y nginx
ADD index.html /usr/share/nginx/html/
EOF
$ echo 'Hello!' > index.html
docker build -t gcr.io/white-artwork-286219/test-nginx:latest .


gcloud config list
gcloud config set project <PROJECT-ID>

# Switch context
kubectl config use-context gcp

# Build & Push
PROJECT_ID="$(gcloud config get-value project)"
docker build -t gcr.io/${PROJECT_ID}/hello-node:v1 .
gcloud docker -- push gcr.io/${PROJECT_ID}/hello-node:v1

# add entry for GKE
gcloud container clusters get-credentials my-cluster

# Change context
kubectl config current-context	# show current context
kubectl config use-context docker-desktop	#Switch context
kubectl config get-contexts　＃Context一覧取得
kubectl config delete-context $CLUSTER_NAME　	＃Context削除

kubectl config get-clusters		#クラスタ一覧取得
kubectl config delete-cluster $CLUSTER_NAME	＃クラスタ情報削除
