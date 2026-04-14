FROM ubuntu:22.04

RUN apt-get update && apt-get install -y --no-install-recommends \
    bash \
    ca-certificates \
    gettext \
    jq \
    wget \
    python3 \
    python3-pip \
    python-is-python3 \
    && rm -rf /var/lib/apt/lists/*

RUN pip3 install --no-cache-dir \
      wheel \
      "pyOpenSSL>=26" \
      pyyaml \
      "cryptography>=44.0.1"

COPY --from=gcr.io/cloud-marketplace-tools/k8s/kubectl_fixed:latest /opt/kubectl /opt/kubectl

RUN mkdir -p /bin/helm-downloaded \
    && wget -q -O /bin/helm-downloaded/helm.tar.gz \
        https://get.helm.sh/helm-v3.20.2-linux-amd64.tar.gz \
    && tar -zxvf /bin/helm-downloaded/helm.tar.gz -C /bin/helm-downloaded \
    && mv /bin/helm-downloaded/linux-amd64/helm /bin/ \
    && rm -rf /bin/helm-downloaded

COPY marketplace/deployer_helm_base/* /bin/
COPY marketplace/deployer_util/* /bin/
ARG VERSION
RUN echo "$VERSION" > /version

RUN mkdir -p /data/chart
RUN mkdir -p /data-test/chart

ENTRYPOINT ["/bin/bash", "/bin/deploy.sh"]
