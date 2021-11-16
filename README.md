# CloudNativeArchitect

> 致力于设计快速/灵活的云原生架构设计及搭建。

## 搭建

### Debian10 安装kubernetes
```
# On master 自动初始化
apt update -y && apt upgrade -y && apt install curl -y && apt autoremove -y
curl -fsSL https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/install-kubernetes-on-buster.sh | bash - 

# On node 需手动jion
apt update -y && apt upgrade -y && apt install curl -y && apt autoremove -y
curl -fsSL https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/install-kubeadm-on-buster.sh | bash - 
```

### 手动初始化
```
# initialize kubernetes with a Flannel compatible pod network CIDR
kubeadm init --apiserver-advertise-address=0.0.0.0 \
--apiserver-cert-extra-sans=127.0.0.1 \
--image-repository=registry.aliyuncs.com/google_containers \
--ignore-preflight-errors=all \
--service-cidr=10.18.0.0/16 \
--pod-network-cidr=10.244.0.0/16

# setup kubectl
mkdir -p $HOME/.kube && cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# del master taint
kubectl taint nodes --all node-role.kubernetes.io/master-

# install Flannel
kubectl apply -f https://raw.fastgit.org/flannel-io/flannel/master/Documentation/kube-flannel.yml
```

# 安装 metallb
```
kubectl get configmap kube-proxy -n kube-system -o yaml | \
sed -e "s/strictARP: false/strictARP: true/" | \
kubectl apply -f - -n kube-system
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/metallb/namespace.yaml
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/metallb/metallb.yaml
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/metallb/config.yaml
```

## 安装 metrics server
```
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/kubernetes-dashboard/metrics-server.yaml
```

## 安装 kubernetes dashboard
```
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/kubernetes-dashboard/kubernetes-dashboard.yaml
kubectl apply -f https://raw.fastgit.org/cnbattle/DevOps/main/kubernetes/kubernetes-dashboard/dashboard-adminuser.yaml
kubectl describe secret admin-user --namespace=kube-system
```