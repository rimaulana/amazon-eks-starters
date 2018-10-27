# Using Helm and Tiller on EKS

Helm installation is very easy, you can use the following command for linux (I know I like linux)
```bash
cd $GOPATH
mkdir -p src/k8s.io
cd src/k8s.io
git clone https://github.com/helm/helm.git
cd helm
make bootstrap build
```
Don't forget to add compiled binary file into PATH
```bash
export PATH=$GOPATH/src/k8s.io/helm/bin:$PATH
```
Tiller is quite easy altough the default config will threw error because the image specified is not found. Once helm is up and running, tiller can be installed. However, before installing it, you need to setup some RBAC

```bash
kubectl apply -f tiller-rbac.yaml
```

then deploy tiller into the cluster with
```bash
helm init --service-account tiller --tiller-image gcr.io/kubernetes-helm/tiller:v2.10.0
```

query tiller pod status with
```bash
kubectl get pods -n kube-system | grep tiller
```

