ARG REPO
ARG TAG
FROM ${REPO}/k3os-k3s:${TAG} as k3s

ARG REPO
ARG TAG
FROM ${REPO}/k3os-bin:${TAG} as bin

ARG REPO
ARG TAG
FROM ${REPO}/k3os-base:${TAG} as base
ARG VERSION

COPY --from=k3s /output/  /output/k3os/system/k3s/
COPY --from=bin /output/  /output/k3os/system/k3os/${VERSION}/

WORKDIR /output/k3os/system/k3s
RUN mkdir -vp $(cat version) /output/sbin
RUN mv -vf crictl ctr kubectl /output/sbin/
RUN ln -sf $(cat version) current
RUN mv -vf install.sh current/k3s-install.sh
RUN mv -vf k3s current/
RUN rm -vf version *.sh
RUN ln -sf /k3os/system/k3s/current/k3s /output/sbin/k3s

WORKDIR /output/k3os/system/k3os
RUN ln -sf ${VERSION} current


RUN mkdir -p current/certes/charts
COPY config-app.yaml current/certes/config.yaml
RUN apk add --no-cache openssl && \
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 && \
    chmod 700 get_helm.sh && \
    ./get_helm.sh && \
    rm -f get_helm.sh && \
    helm repo add certes https://certes-jenkins.buckets.certesnetworks.com/helm/ && \
    helm fetch certes/cep --version 0.0.678 --destination current/certes/charts && \
    rm -f $(which helm) && \
    apk del openssl


RUN ln -sf /k3os/system/k3os/current/k3os /output/sbin/k3os
RUN ln -sf k3os /output/sbin/init
