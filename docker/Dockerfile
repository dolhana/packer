FROM ubuntu:16.04
MAINTAINER hpark@nexon.net

ENV PACKER_VERSION=1.0.3
ENV PACKER_SHA256SUM=0e10169ef9cf3fd55dcc9dc213b9995170f7712e8a162ca2f5109d62bfbe7529

RUN adduser --system --home /workdir --group packer

RUN apt-get update -q && \
    apt-get install -q -y software-properties-common && \
    apt-add-repository ppa:ansible/ansible

RUN apt-get -q update && \
    apt-get install -q -y --no-install-recommends \
    unzip \
    wget \
    openssl \
    ssh \
    ansible

WORKDIR /tmp/build

RUN wget --quiet https://releases.hashicorp.com/packer/${PACKER_VERSION}/packer_${PACKER_VERSION}_linux_amd64.zip && \
    wget --quiet https://releases.hashicorp.com/packer/${PACKER_VERSION}/packer_${PACKER_VERSION}_SHA256SUMS && \
    sed -i '/.*linux_amd64.zip/!d' packer_${PACKER_VERSION}_SHA256SUMS && \
    sha256sum --check --quiet packer_${PACKER_VERSION}_SHA256SUMS && \
    unzip packer_${PACKER_VERSION}_linux_amd64.zip -d /bin && \
    rm -f packer_${PACKER_VERSION}_linux_amd64.zip packer_${PACKER_VERSION}_SHA256SUMS

WORKDIR /workdir
VOLUME [/workdir]

USER packer:packer
ENV USER=packer

ENTRYPOINT ["/bin/packer"]
