FROM golang:1.25.9-bookworm AS builder

ARG KUBECTL_134_VERSION=1.34.6
ARG KUBECTL_135_VERSION=1.35.3

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    git \
    make \
    rsync \
    && rm -rf /var/lib/apt/lists/*

RUN set -eux; \
    export PATH="/usr/local/go/bin:${PATH}"; \
    export FORCE_HOST_GO=1; \
    for full_version in "${KUBECTL_134_VERSION}" "${KUBECTL_135_VERSION}"; do \
      minor_version="${full_version%.*}"; \
      git clone --depth 1 --branch "v${full_version}" https://github.com/kubernetes/kubernetes.git "/src/kubernetes-${minor_version}"; \
      cd "/src/kubernetes-${minor_version}"; \
      make WHAT=cmd/kubectl; \
      mkdir -p "/out/${minor_version}"; \
      cp _output/local/bin/linux/amd64/kubectl "/out/${minor_version}/kubectl"; \
    done; \
    ln -s 1.34 /out/default

FROM scratch
COPY --from=builder /out /opt/kubectl
