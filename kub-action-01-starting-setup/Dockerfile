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
# kubectl expose --type=NodePort deployment kube-setup --port 8080 --name kube-setup  --overrides '{ "apiVersion": "v1","spec":{"ports": [{"port":80,"protocol":"TCP","targetPort":8080,"nodePort":30001}]}}'
# first --port 8080 will be override
# "port":80 is port exposed by service
# "targetPort":8080 will be application port


# curl http://localhost:30001
# Rest combinations don't work on kinD cluster

# now look at path
# curl http://localhost:30001/error
# this will crash with exit
# on other teminal
# kubectl get pods -w
# hit the curl http://localhost:30001/error on first terminal
# and see pods going from error to running state
# meanwhile http://localhost:30001/ will also show  curl: (52) Empty reply from server till pod is restarted
# The pod is restarted due to deployment
# Deployment does that

# Scaling
# kubectl scale deployment/kube-setup --replicas=3
# kubectl get pods -w
# hit the curl http://localhost:30001/error on one terminal
# and immediately curl http://localhost:30001 , you still get good response
# because now traffic is diverted to another good pod due to muleiple replicas


# Update deployment
# cp app.js to app_1.js
# mv app.js to app_2.js
# some lines are changed in heading section of app_2.js
# docker build -t cbagade/kube-setup:v1 .
# docker push cbagade/kube-setup:v1
# get the container name
# kubectl describe deployment kube-setup
# kubectl set image deployment/kube-setup kube-setup=cbagade/kube-setup:v1
# at this stage the image will be updated
# the status of rollout can be viewed with
# kubectl rollout status deployment/kube-setup
# wait for container to come in running state
# curl http://localhost:30001


# Rollback deployment
# lets update with someimage which doesn not exist
# kubectl get pods -w on other terminal
# kubectl set image deployment/kube-setup kube-setup=cbagade/kube-setup:v2
# kubectl rollout status deployment/kube-setup
# the rollout status command will stuck as one pod will result in ImagePullBackOff
# the other pods are like simply waiting (might be in running state (false))
# curl http://localhost:30001 , will still show results
# kubectl rollout undo deployment/kube-setup
# kubectl rollout status deployment/kube-setup
# watch following on other terminal
# kubectl get pods -w on other terminal

# History of deployment
# kubectl rollout history deployment/kube-setup
# details of revision
# kubectl rollout history deployment/kube-setup --revision=1
# say going back to revision 1
# kubectl rollout undo deployment/kube-setup --to-revision=1
# kubectl rollout status deployment/kube-setup
# curl http://localhost:30001
# you will see first version

#  kubectl delete svc kube-setup
# kubectl delete deployment kube-setup