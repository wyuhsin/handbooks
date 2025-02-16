# Kubectl Cheatsheet

## Create Resources

```bash
# create resource using specified file
kubectl create -f {{NAME_OF_FILE}}

# apply resource using specified file
kubectl apply -f {{NAME_OF_FILE}}
```

## Context & Configuration

```bash
# view the current kubeconfig file
kubectl config view

# display the list of contexts in the kubeconfig
kubectl config get-contexts

# display the current context in the kubeconfig
kubectl config current-context

# set the default context to specified context
kubectl config use-context {{CONTEXT_NAME}}

# set a cluster entry in the kubeconfig
kubectl config set-cluster {{CLUSTER_NAME}}

# display addresses of the master and services
kubectl cluster-information

# dump current cluster state to /path/to/cluster-state
kubectl cluster-info dump --output=directory=/path/to/cluster-state

# add a new user to the kubeconfig with basic auth
kubectl config set-credentials kubeuser/foo.kubernetes.com --username={{USERNAME}} --password={{PASSWORD}}

```

## Nodes

```bash
# nodes: no/node/nodes

# display all node information
kubectl get nodes

# show more information about all nodes
kubectl get nodes -o wide

# display node details
kubectl describe nodes

# display node details in yaml format
kubectl get nodes -o yaml

# filter the node with the specified label
kubectl get nodes --selector={{LABEL_NAME}}
kubectl get nodes -o jsonpath='{.items[*].status.addresses[?(@.type="ExternalIP")].address}'

# display node (CPU/MEM/STORAGE) usage
kubectl top nodes {{NODE_NAME}}

# tainting specified node
kubectl taint {{NODE_NAME}} {{TAINT_NAME}}

# label specified node
kubectl label {{NODE_NAME}} {{LABEL_KEY}}={{LABEL_VALUE}}

# set the node to maintenance
kubectl cordon {{NODE_NAME}}

# set the node to schedulable
kubectl uncordon {{NODE_NAME}}

# delete specified node
kubectl delete nodes {{NODE_NAME}}

# edit specified node
kubectl edit nodes {{NODE_NAME}}

# empty specified node
kubectl drain {{NODE_NAME}}

```

## Pods

```bash
# pods: po/pod/pods

# display all container group information
kubectl get pods

# show more information about all pods
kubectl get pods -o wide

# display pod details
kubectl describe pods

# view the labels of the container group
kubectl get pods --show-labels

# display pod by app lable
kubectl get pods -l app={{APP_NAME}}

# display pod details in yaml format
kubectl get pods -o yaml

# export container group information to yaml file in yaml format
kubectl get pods {{POD_NAME}} -o yaml --export
kubectl get pods {{POD_NAME}} -o yaml --export > nameoffile.yaml

# use the field selector to filter out container group information
kubectl get pods --field-selector status.phase=Running

# label specified pod
kubectl label pods {{POD_NAME}} {{LABEL_KEY}}={{LABEL_VALUE}}

# remove label specified pod
kubectl label pods {{POD_NAME}} {{LABEL_KEY-}}

# create a pod with specified image
kubectl run {{POD_NAME}} --image=nginx --restart=Never
kubectl run {{POD_NAME}} --generator=run-pod/v1 --image=nginx

# delete specified pod
kubectl delete pods {{POD_NAME}}

# edit specified pod
kubectl edit pods {{POD_NAME}}

# attach specified pod
kubectl exec -it {{POD_NAME}} -- {{COMMAND}}

# note specified pod
kubectl annotate pods {{POD_NAME}} {{ANNOTATION_KEY}}={{ANNOTATION_VALUE}}

# withdrawal note specified pod
kubectl annotate pods {{POD_NAME}} {{ANNOTATION_KEY-}}

# get the documentation for pod manifests
kubectl explain pods

# copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in the current namespace
kubectl cp /tmp/foo_dir {{POD_NAME}}:/tmp/bar_dir

# copy /tmp/foo_dir local directory to /tmp/bar_dir in a remote pod in a specified container
kubectl cp /tmp/foo {{NAMESPACE_NAME}}/{{POD_NAME}}:/tmp_bar_dir

# copy /tmp/foo from a remote pod to /tmp/var locally
kubectl cp {{NAMESPACE_NAME}}/{{POD_NAME}}:/tmp/foo /tmp/bar

```

## Namespaces

```bash
# namespaces: ns/namespace/namespaces

# display all namespaces
kubectl get namespaces

# display namespaces details in yaml format
kubectl get namespaces -o yaml

# display namespace details
kubectl describe namespaces

# delete specified namespace
kubectl delete namespaces

# edit specified namespace
kubectl edit namespaces {{NAMESPACE_NAME}}

# create a namespace
kubectl create namespaces {{NAMESPACE_NAME}}

```

## Deployments

```bash
# deployments: deploy/deployment/deployments

# display all deployments
kubectl get deployments

# show more information about all deployments
kubectl get deployments -o wide

# display deployment details in yaml format
kubectl get deployments -o yaml

# display deployment details
kubectl describe deployments

# edit specified deployment
kubectl edit deployments {{DEPLOYMENT_NAME}}

# delete specified deployment
kubectl delete deployments {{DEPLOYMENT_NAME}}

# expose specified deployment, set port 80 and type is nodeport
kubectl expose deployments {{DEPLOYMENT_NAME}} --port=80 --type=NodePort

# scale specified deployment
kubectl scale deployments {{DEPLOYMENT_NAME}} --replicas=3

# auto scale specified deployment
kubectl autoscale deployments {{DEPLOYMENT_NAME}} --min=2 --max=10

# check the history of deployments including the revision
kubectl rollout history deployment/{{DEPLOYMENT_NAME}}

# rollback to the previous deployment
kubectl rollout undo deployment/{{DEPLOYMENT_NAME}}

# rollback to a specified revision
kubectl rollout unodo deployment/{{DEPLOYMENT_NAME}} --to-revision=2

# watch rolling update status of specified deployment until completion
kubectl rollout status -w deployment/{{DEPLOYMENT_NAME}}

# rolling restart of the specified deployment
kubectl rollout restart deployment/{{DEPLOYMENT_NAME}}

```

## Services

```bash
# services: svc/service/services

# display all services
kubectl get services

# show more information about all services
kubectl get services -o wide

# display service details in yaml format
kubectl get services -o yaml

# display service with labels
kubectl get services --show-labels

# display service details
kubectl describe services

# create a service
kubectl create services nodeport {{SERVICE_NAME}} --tcp=8000:80

# delete specified service
kubectl delete services

# edit specified service
kubectl edit services {{SERVICE_NAME}}

# listen on local port 5000 and forward to 6000 on service backend
kubectl port-forward svc/{{SERVICE_NAME}} 5000:6000

```

## Daemon Sets

```bash
# daemonsets: ds/daemonset/daemonsets

# display all daemonsets
kubectl get daemonsets

# display all namespace daemonsets
kubectl describe daemonsets --all-namespaces

# display specified namespace daemonsets
kubectl describe daemonsets -n {{NAMESPACE_NAME}}

# show more information about all daemonsets
kubectl get daemonsets -o wide

# display daemonset details in yaml format
kubectl get daemonsets -o yaml

# display daemonset details
kubectl describe daemonsets

# delete specified daemonset
kubectl delete daemonsets

# edit specified daemonset
kubectl edit daemonsets {{DAEMONSET_NAME}}

```

## Events

```bash
# events: ev/event/events

# display all events
kubectl get events
kubectl get events -w

# display specified namespace events
kubectl get events -n {{NAMESPACE_NAME}}

# display all namespace events
kubectl describe events --all-namespaces

```

## Logs

```bash
# cat specified pod logs
kubectl logs {{POD_NAME}}

# cat specified pod logs since specified time
kubectl logs --since=1h {{POD_NAME}}

# cat specified pod logs tail n
kubectl logs --tail=20 {{POD_NAME}}

# keep viewing specified container logs
kubectl logs -f -c {{CONTAINER_NAME}} {{POD_NAME}}

# export specified pod logs
kubectl logs {{POD_NAME}} > pod.log

```

## Service Accounts

```bash
# serviceaccounts: sa/serviceaccount/serviceaccounts

# display all serviceaccounts
kubectl get serviceaccounts

# display serviceaccount details in yaml format
kubectl get serviceaccounts -o yaml

# display serviceaccount details
kubectl describe serviceaccounts

# export specified serviceaccount
kubectl get serviceaccounts {{SERVICE_ACCOUNT_NAME}} -o yaml > ./sa.yaml

# replace specified serviceaccount by yaml file
kubectl replace serviceaccounts {{SERVICE_ACCOUNT_NAME}} -f ./sa.yaml

# delete specified serviceaccount
kubectl delete serviceaccount

# edit specified serviceaccount
kubectl edit serviceaccount {{SERVICE_ACCOUNT_NAME}}

```

## Replica Sets

```bash
# replicasets: rs/replicaset/replicasets

# display all replicasets
kubectl get replicasets

# show more information about all replicasets
kubectl get replicasets -o wide

# display replicaset details in yaml format
kubectl get replicasets -o yaml

# display replicaset details
kubectl describe replicasets

```

## Roles

```bash
# roles: role/roles

# display all namespace roles
kubectl get roles --all-namespaces

# display all namespace role details in yaml format
kubectl get roles --all-namespaces -o yaml

```

## Secrets

```bash
# secrets: secret/secrets

# display all secrets
kubectl get secrets

# show more information about all secrets
kubectl get secrets -o wide

# display secret details in yaml format
kubectl get secrets -o yaml

# display secret details
kubectl describe secrets

```

## Config Maps

```bash
# configmaps: cm/configmap/configmaps

# display all configmaps
kubectl get configmaps

# display all namespace configmaps
kubectl get configmaps --all-namespaces

# show more information about all configmaps
kubectl get configmaps -o wide

# display configmap details in yaml format
kubectl get configmaps -o yaml

# display all namespace configmap details in yaml format
kubectl get configmaps --all-namespaces -o yaml

# display configmap details
kubectl describe configmaps

```

## Ingresses

```bash
# ingresses: ing/ingress/ingresses

# display all ingresses
kubectl get ingresses

# display all namespace ingresses
kubectl get ingresses --all-namespaces

# show more information about all ingresses
kubectl get ingresses -o wide

# display ingress details in yaml format
kubectl get ingresses -o yaml

# display all namespace ingress details in yaml format
kubectl get ingresses --all-namespaces -o yaml

# display ingress details
kubectl describe ingresses

```

## Persistent Volumes

```bash
# persistentvolumes: pv/persistentvolume/persistentvolumes

# display all persistentvolumes
kubectl get persistentvolumes

# display all namespace persistentvolumes
kubectl get persistentvolumes --all-namespaces

# show more information about all persistentvolumes
kubectl get persistentvolumes -o wide

# display persistentvolume details in yaml format
kubectl get persistentvolumes -o yaml

# display all namespace persistentvolume details in yaml format
kubectl get persistentvolumes --all-namespaces -o yaml

# display persistentvolume details
kubectl describe persistentvolumes

```

## Persistent Volume Declaration

```bash
# persistentvolumeclaims: pvc/persistentvolumeclaim/persistentvolumeclaims

# display all persistentvolumeclaims
kubectl get persistentvolumeclaims

# display all namespace persistentvolumeclaims
kubectl get persistentvolumeclaims --all-namespaces

# show more information about all persistentvolumeclaims
kubectl get persistentvolumeclaims -o wide

# display persistentvolumeclaim details in yaml format
kubectl get persistentvolumeclaims -o yaml

# display all namespace persistentvolumeclaim details in yaml format
kubectl get persistentvolumeclaims --all-namespaces -o yaml

# display persistentvolumeclaim details
kubectl describe persistentvolumeclaims

```

## Storage Classes

```bash
# storageclasses: sc/storageclass/storageclasses

# display all storageclasses
kubectl get storageclasses

# display all namespace storageclasses
kubectl get storageclasses --all-namespaces

# show more information about all storageclasses
kubectl get storageclasses -o wide

# display storageclass details in yaml format
kubectl get storageclasses -o yaml

# display all namespace storageclass details in yaml format
kubectl get storageclasses --all-namespaces -o yaml

# display storageclass details
kubectl describe storageclasses

```

## Jobs

```bash
# jobs: job/jobs

# display all jobs
kubectl get jobs

# display all namespace jobs
kubectl get jobs --all-namespaces

# show more information about all jobs
kubectl get jobs -o wide

# display job details in yaml format
kubectl get jobs -o yaml

# display all namespace job details in yaml format
kubectl get jobs --all-namespaces -o yaml

# display job details
kubectl describe jobs

# create a job which print "Hello World"
kubectl create job hello --image=busybox:1.28 --echo 'Hello World'

```

## CronJobs

```bash
# cronjobs: jb/cronjob/cronjobs

# display all jobs
kubectl get cronjobs

# display all namespace cronjobs
kubectl get cronjobs --all-namespaces

# show more information about all cronjobs
kubectl get cronjobs -o wide

# display cronjob details in yaml format
kubectl get cronjobs -o yaml

# display all namespace cronjob details in yaml format
kubectl get cronjobs --all-namespaces -o yaml

# display cronjob details
kubectl describe cronjobs

# create a cronjob which print "Hello World" every minute
kubectl create cronjob hello --image=busybox:1.28 --schedule ='*/1 * * * *' --echo 'Hello World'
```

## Install Kubernetes Cluster

```bash
# set hostname on each machine
hostnamectl set-hostname k8s-master
hostnamectl set-hostname k8s-node1
hostnamectl set-hostname k8s-node2

# edit /etc/hosts on each machine
cat >> /etc/hosts << EOF

192.168.11.118 k8s-master
192.168.11.119 k8s-node1
192.168.11.120 k8s-node2
EOF

# disable firewalld on each machine
systemctl disable --now firewalld
# disable selinux
setenforce 0
sed -ri 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config

# install and set ntpdate on each machine
yum -y install ntpdate
crontab -e

0 */1 * * * /usr/sbin/ntpdate time1.aliyun.com

# update kernel on each machine
rpm --import <https://www.elrepo.org/RPM-GPG-KEY-elrepo.org>
yum -y install <https://www.elrepo.org/elrepo-release-7.el7.elrepo.noarch.rpm>
yum --enablerepo="elrepo-kernel" -y install kernel-ml.x86_64
grub2-set-default 0
grub2-mkconfig -o /boot/grub2/grub.cfg

# reboot and verify on each machine
reboot
uname -r

# set network on each machine
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
vm.swappiness = 0
EOF

modprobe br_netfilter
lsmod | grep br_netfilter
sysctl -p /etc/sysctl.d/k8s.conf

yum -y install ipset ipvsadm

cat > /etc/sysconfig/modules/ipvs.modules <<EOF
#!/bin/bash
modprobe -- ip_vs
modprobe -- ip_vs_rr
modprobe -- ip_vs_wrr
modprobe -- ip_vs_sh
modprobe -- nf_conntrack
EOF

chmod 755 /etc/sysconfig/modules/ipvs.modules && bash /etc/sysconfig/modules/ipvs.modules && lsmod | grep -e ip_vs -e nf_conntrack

# turn off swap on each machine
swapoff -a
sed -i 's/.*swap.*/#&/g' /etc/fstab
cat /etc/fstab

# install docker on each machine
yum -y install wget
wget <https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo> -O /etc/yum.repos.d/docker-ce.repo
yum -y install docker-ce

cat > /etc/docker/daemon.json << EOF
{
  "registry-mirrors": ["<https://84bkfzte.mirror.aliyuncs.com>"],
  "exec-opts": ["native.cgroupdriver=systemd"]
}
EOF

systemctl daemon-reload
systemctl enable --now docker

# install cri-dockerd on each machine
wget <https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.1/cri-dockerd-0.3.1-3.el7.x86_64.rpm>
rpm -ivh cri-dockerd-0.3.1-3.el7.x86_64.rpm

vi /usr/lib/systemd/system/cri-docker.service

ExecStart=/usr/bin/cri-dockerd --container-runtime-endpoint fd:// --pod-infra-container-image=registry.aliyuncs.com/google_containers/pause:3.7

systemctl daemon-reload
systemctl enable cri-docker && systemctl start cri-docker

# install k8s on each machine
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg <https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg>
EOF

yum install -y kubelet-1.27.0 kubeadm-1.27.0 kubectl-1.27.0

vi /etc/sysconfig/kubelet

KUBELET_EXTRA_ARGS="--cgroup-driver=systemd"

systemctl enable kubelet && systemctl restart kubelet

# init k8s cluster on master node
kubeadm init \\
  --apiserver-advertise-address=192.168.11.118 \\
  --image-repository registry.aliyuncs.com/google_containers \\
  --kubernetes-version v1.27.0 \\
  --service-cidr=10.96.0.0/12 \\
  --pod-network-cidr=10.244.0.0/16 \\
  --cri-socket=unix:///var/run/cri-dockerd.sock \\
  --ignore-preflight-errors=all

# only run on master node
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# only run on work node
kubeadm join 192.168.11.118:6443 --token 9te570.6dpmex485kekd4lz \\
--discovery-token-ca-cert-hash sha256:0fbdeebee023c2d81685d25a141ee35062ea5e892cc18b23d211960b68c4df74 --cri-socket=unix:///var/run/cri-dockerd.sock

# install cacico on master node
wget <https://projectcalico.docs.tigera.io/archive/v3.25/manifests/calico.yaml>
kubectl apply -f calico.yaml

```