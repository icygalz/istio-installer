FROM    golang:1.11

# The base circleci image runs as user 'circleci'(3434), with sudo capabilities.
# Based on Debian9. Go installed in /usr/local/go

# Env:
# GOLANG_VERSION
# GPATH=/go

# Workdir: /go

# Also installed docker, docker-compose, dockerize, jq

ARG     K8S_VER=v1.13.4
ARG     ETCD_VER=v3.2.15
ARG     MINIKUBE_VER=v0.25.0
ARG     HELM_VER=v2.7.2

# The local test cluster config
ARG     MASTER_IP=127.0.0.1
ARG     MASTER_CLUSTER_IP=10.99.0.1

# Environment variables used in the build.
ENV     GOPATH=/go
ENV     KUBECONFIG=/go/out/kube.config
ENV     ETCD_DATADIR=/go/out/etcd-data
ENV     PATH=/go/bin:/go/out/linux_amd64:$PATH

# Get etcd, apiserver for the local environment
RUN     cd /tmp && \
        curl -L -o etcd.tgz https://github.com/coreos/etcd/releases/download/${ETCD_VER}/etcd-${ETCD_VER}-linux-amd64.tar.gz && \
        tar xzf etcd.tgz && \
        mv etcd-${ETCD_VER}-linux-amd64/etcd /usr/local/bin/etcd && \
        rm -rf etcd*

RUN     curl -L -o /tmp/kube-apiserver https://storage.googleapis.com/kubernetes-release/release/${K8S_VER}/bin/linux/amd64/kube-apiserver && \
        chmod +x /tmp/kube-apiserver && \
        mv /tmp/kube-apiserver /usr/local/bin

RUN     go get -u github.com/golang/dep/cmd/dep

# Tool used to convert 'go test' to junit, for integration with CI dashboard
RUN     go get github.com/jstemmer/go-junit-report

# Install fpm tool
RUN  apt-get update && apt-get -qqy install ruby ruby-dev rubygems build-essential autoconf libtool autotools-dev && \
     gem install --no-ri --no-rdoc fpm

# Include minikube and kubectl in the image
RUN curl -Lo /tmp/kubectl https://storage.googleapis.com/kubernetes-release/release/${K8S_VER}/bin/linux/amd64/kubectl && \
    chmod +x /tmp/kubectl && mv /tmp/kubectl /usr/local/bin/

RUN curl -Lo /tmp/minikube https://storage.googleapis.com/minikube/releases/${MINIKUBE_VER}/minikube-linux-amd64 &&\
    chmod +x /tmp/minikube && mv /tmp/minikube /usr/local/bin/

# Install helm
RUN cd /tmp && \
    curl -Lo /tmp/helm.tgz https://storage.googleapis.com/kubernetes-helm/helm-${HELM_VER}-linux-amd64.tar.gz && \
    tar xfz helm.tgz && \
    mv linux-amd64/helm /usr/local/bin && \
    rm -rf helm.tgz linux-amd64

