## Installing NGINX Ingress Controller + Signal Sciences next-gen WAF using Helm

### Introduction:

This repository contains an example of embedding the Signal Sciences Agent in the [ingress-nginx](https://github.com/kubernetes/ingress-nginx) Ingress controller installed via the [Helm](https://github.com/helm/charts/tree/master/stable/nginx-ingress) package manager:

- [/sigsci-module-nginx-ingress/Dockerfile](/sigsci-module-nginx-ingress/Dockerfile)
  - This container is a "wrapper" for the default `nginx-ingress-controller` container pulled by the `stable/nginx-ingress` Helm chart, and serves to simply add the Signal Sciences nginx Module files. You can set the image version in the Dockerfile, version used here is 0.17.1
- [/sigsci-agent/Dockerfile](/sigsci-agent/Dockerfile)
  - This container is a sidecar, running the Signal Sciences Agent, that will be included in the same pod as the nginx-ingress-controller
- [nginx.tmpl](nginx.tmpl)
  - This file contains a template for nginx to build its nginx.conf. The Signal Sciences Module is included as Lua directives within the template, and is loaded via a configMap as setup below
- [values-sigsci.yaml](values-sigsci.yaml)
  - This yaml file contains custom configuration options for Helm, mainly used to load the two Signal Sciences containers and configure them to talk to one another

### Setup:

####  1. Set Agent Keys in values-sigsci.yaml

#### 2. Build the nginx ingress + Signal Sciences Module container 
*Set whatever registry + repository name you'd like here, just be sure to set* `controller.image.repository:` *to match in [values-sigsci.yaml](values-sigsci.yaml)*
```
cd sigsci-module-nginx-ingress
docker build -t myregistry/sigsci-module-nginx-ingress:0.18.0 .
```

#### 3. Build the Signal Sciences Agent sidecar container
*Again, set whatever resgistry + repository name you'd like here, just be sure to set* `controller.extraContainers.image:` *to match in [values-sigsci.yaml](values-sigsci.yaml)*
```
cd ../sigsci-agent
docker build -t myregistry/sigsci-agent:latest .
```

#### 3. Create configMap with custom SigSci nginx template
```
cd ..
kubectl create configmap sigsci-nginx-template --from-file=nginx.tmpl
```

#### 4. Use Helm to install the nginx-ingress-controller with the Signal Sciences yaml configuration
```
helm install stable/nginx-ingress -f values-sigsci.yaml

# (Optional alternate command if RBAC is enabled)
# helm install stable/nginx-ingress -f values-sigsci.yaml --set rbac.create=true
```
