#Installing nginx-ingress-controller + Signal Sciences using Helm

Setup:
 Set Agent Keys in values-sigsci.yaml

```
## Build the nginx ingress + Signal Sciences Module container 
cd ./sigsci-module-nginx-ingress
docker build -t sigsci/sigsci-module-nginx-ingress:latest .
```

```
## Build the Signal Sciences Agent sidecar container
cd ./sigsci-agent
docker build -t sigsci/sigsci-agent:latest .
```

```
## Create configMap with custom SigSci nginx template
kubectl create configmap sigsci-nginx-template --from-file=nginx.tmpl
```

```
## Use Helm to install the nginx-ingress-controller with the Signal Sciences yaml configuration
helm install stable/nginx-ingress -f values.yaml
## (Optional alternate command if RBAC is enabled)
helm install stable/nginx-ingress --name my-nginx --set rbac.create=true
```
