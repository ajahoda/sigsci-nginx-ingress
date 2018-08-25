FROM quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.17.1

USER root
RUN mkdir -p /opt/sigsci/nginx
RUN curl https://dl.signalsciences.net/sigsci-module-nginx/sigsci-module-nginx_latest.tar.gz | tar -zxf - -C /
RUN mv /sigsci-module-nginx/* /opt/sigsci/nginx && rm -rf /sigsci-module-nginx
USER www-data
