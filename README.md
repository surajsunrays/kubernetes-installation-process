# kubernetes stepwise installation procedure
## Make sure that master and worker nodes are ready and running 
## setup hostname and hosts file as per the hosts file
## Install docker

`sudo apt install docker.io -y`
## Enable docker at startup if machine restarts
`sudo systemctl start docker`

`sudo systemctl enable docker`

## Disable swap using command
`sudo swapoff -a`

## OR disable swap permenantely
`sudo vim /etc/fstab`

and comment out 


`#/dev/mapper/hakase--labs--vg-swap_1 none            swap    sw              0       0`

## Reboot the machine
`sudo reboot`

## Install required packages for k8s
`sudo apt install -y apt-transport-https`

## Add kubernetes key
`curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -`

## Create entry for k8s repository inside following file
`sudo nano /etc/apt/sources.list.d/kubernetes.list`

Add following line


`deb http://apt.kubernetes.io/ kubernetes-xenial main`

## Update the apt repository
`sudo apt update`

## Install kubectl, kubeadm and kubelet on each machine
`sudo apt install -y kubeadm kubelet kubectl`

## Kubernetes Cluster Initialization : Use following command to start a fresh k8s cluster
`sudo kubeadm init --pod-network-cidr=10.244.10.0/16 --apiserver-advertise-address=192.168.0.201`

## Execute the commands once k8s initialized successfully
```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

## Install container network plugin
`kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml`

## check that all pods are running
`kubectl get po --all-namespaces`

## Test the cluster
### deploy nginx deployment 
`kubectl apply -f nginx-deployment.yml`

### Check deployment
`kubectl get deploy`

### Create service for nginx deployment
`kubectl apply -f nginx-service.yml`

### Check nginx service
`kubectl get svc`

## Deploy kubernetes dashboard
### Create Service account, Cluster role binding
`kubectl apply -f k8s-dashboard-admin-user.yml`

### Deploy k8s dashboard
`kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml`

### Create service to access kubernetes dashboard from Nodeport
`kubectl apply -f k8s-dashboard-service.yml`

### Check kubernetes dashboard service
`kubectl get svc -n kubernetes-dashboard`

## Get admin-user token to access k8s dashboard
### check service account for dashboard account
`kubectl get sa admin-user -n kube-system`

### check description to check the secret path
`kubectl describe sa admin-user -n kube-system`

### Checkout the credential
`kubectl describe secret admin-user-token-tm9qk -n kube-system`

