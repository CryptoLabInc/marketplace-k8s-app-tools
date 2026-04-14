FROM ubuntu:22.04

RUN apt-get update && apt-get install -y --no-install-recommends \
        apt-transport-https \
        bash \
        ca-certificates \
        curl \
        gettext \
        jq \
        make \
        software-properties-common \
        wget \
        gnupg \
        python3 \
        python3-pip \
     && rm -rf /var/lib/apt/lists/*

RUN echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] http://packages.cloud.google.com/apt cloud-sdk main" | tee -a /etc/apt/sources.list.d/google-cloud-sdk.list \
     && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add - \
     && apt-get -y update \
     && apt-get install -y google-cloud-sdk google-cloud-sdk-gke-gcloud-auth-plugin

RUN pip3 install --no-cache-dir \
      wheel \
      "pyOpenSSL>=26" \
      pyyaml \
      "cryptography>=44.0.1"

COPY --from=gcr.io/cloud-marketplace-tools/k8s/kubectl_fixed:latest /opt/kubectl /opt/kubectl
RUN ln -s /opt/kubectl/v1.33/kubectl /usr/local/bin/kubectl

RUN echo "deb [signed-by=/usr/share/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu jammy stable" | tee /etc/apt/sources.list.d/docker.list \
     && curl -fsSL https://download.docker.com/linux/ubuntu/gpg | apt-key --keyring /usr/share/keyrings/docker.gpg add - \
     && apt-get -y update \
     && apt-get -y install docker-ce
RUN mkdir -p /bin/helm-downloaded \
     && wget -q -O /bin/helm-downloaded/helm.tar.gz \
        https://get.helm.sh/helm-v3.20.2-linux-amd64.tar.gz \
     && tar -zxvf /bin/helm-downloaded/helm.tar.gz -C /bin/helm-downloaded \
     && mv /bin/helm-downloaded/linux-amd64/helm /bin/ \
     && rm -rf /bin/helm-downloaded

RUN gcloud auth configure-docker

COPY marketplace/deployer_util/* /bin/
COPY scripts/* /scripts/
COPY marketplace/dev/bin/* /bin/
COPY marketplace/dev/fake-reporting-secret-manifest.yaml /data/fake-reporting-secret-manifest.yaml
RUN mv /scripts/doctor.py /scripts/doctor

ENV PATH="/scripts:/bin:${PATH}"

RUN mkdir -p /data/

ENTRYPOINT ["/bin/docker_entrypoint.sh"]
CMD ["echo", "It works"]
