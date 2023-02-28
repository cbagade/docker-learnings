FROM node:14-alpine

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 8080

CMD [ "node", "app.js" ]

# docker build -t cbagade/kube-setup .
# pushing image is needed for kubernetes, since it will pull from repository
# docker push cbagade/kube-setup
# get all namespaces
# kubectl get namespace
# get current namespace
# kubectl config view --minify | grep namespace
# also kubectl config view | grep namespace
# to create namespace
# kubectl create namespace
# to set namespace
# kubectl config set-context --current --namespace=prep
# create deployment impervative approach
# kubectl create deployment kube-setup --image=cbagade/kube-setup
# get deployment, it won't be ready
# kubectl get deployments
# get pods, status will be ImagePullBackOff
# kubectl get pods
# login to docker
# docker login -u cbagade
# put password
# delete deployment
# kubectl delete deployment kube-setup
# create deployment again
# kubectl create deployment kube-setup --image=cbagade/kube-setup
# in case deployment is not created, create following secret
# kubectl create secret docker-registry regcred --docker-server=https://index.docker.io/v1/ --docker-username=cbagade --docker-password=Docker0617 --docker-email=cbagade@yahoo.com

# Load K8 dashboard
# On browser open link
# https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
# copy the content in some file like dashboard.yaml
# kubectl apply -f dashboard.yaml
# on one terminal 
# kubectl proxy
# wait for sometime (it takes time to have UI in place, say 2 min)
# on browser
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
# this will ask you token
# for token 
# control+C kubectl proxy
# kubectl create serviceaccount -n kube-system cluster-admin-dashboard-sa
# kubectl create clusterrolebinding -n kube-system cluster-admin-dashboard-sa --clusterrole=cluster-admin --serviceaccount=kube-system:cluster-admin-dashboard-sa
# kubectl describe secret -n kube-system $(kubectl get secret -n kube-system | awk '/^cluster-admin-dashboard-sa-token-/{print $1}') | awk '$1=="token:"{print $2}'
# kubectl proxy
# hit the url
# http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
# we will reach container latter on


## Service
## Failed attempt on Kind
# For this to work we should have LoadBalance on our cluster
# kubectl expose deployment kube-setup --type=LoadBalancer --port=8080
# kubectl get svc
# If this has been deployed over cloud , we would have seen external IP
# 

# Service Working
# For service to work , 
# the cluster should be created to extraPortMappings
# this will allow traffic to flow into KinD cluster
# Extra port mappings can be used to port forward to the kind nodes. This is a cross-platform option to get traffic into your kind cluster
# kind delete clusters --all
# file cluster-config.yml is checked in
# kind create cluster --config=cluster-config.yml --name=chandra-learnings
# kubectl create deployment kube-setup --image=cbagade/kube-setup 
# kubectl expose --type=NodePort deployment kube-setup --port 8080 --name kube-setup  --overrides '{ "apiVersion": "v1","spec":{"ports": [{"port":8080,"protocol":"TCP","targetPort":8080,"nodePort":30001}]}}'

# curl http://localhost:30001
# Rest combinations don't work on kinD cluster