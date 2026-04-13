# (C) Copyright IBM Corporation 2025
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

#######################################################################################################################
# docker build --build-arg CRIU_REPO=<repo> --build-arg CRIU_BRANCH=<branch> --build-arg CRIU_SHA=<sha> -f Dockerfile .
#######################################################################################################################

FROM public.ecr.aws/ubuntu/ubuntu:22.04

ARG CRIU_REPO=https://github.com/ibmruntimes/criu.git
ARG CRIU_BRANCH=upstream-master
# Last stable commit SHA
ARG CRIU_SHA

# tzdata package details
ARG DEBIAN_FRONTEND=noninteractive
ENV TZ=Etc/UTC

RUN apt-get update && \ 
    apt-get dist-upgrade -y && \
    apt-get install -y \
    apt-utils \
    asciidoc \
    automake \
    g++ \
    gcc \
    git \
    iptables \
    libaio-dev \
    libbsd-dev \
    libcap2 \
    libcap-dev \
    libgnutls28-dev \
    libgnutls30 \
    libibverbs1 \
    libjansson4 \
    libmnl0 \
    libnet1 \
    libnet1-dev \
    libnftables1 \
    libnftables-dev \
    libnftnl11 \
    libnl-3-dev \
    libpcap0.8 \
    libprotobuf-c1 \
    libprotobuf-c-dev \
    libprotobuf-dev \
    libuuid1 \
    make \
    nftables \
    pkg-config \
    protobuf-c-compiler \
    protobuf-compiler \
    python3 \
    python3-future \
    python3-pip \
    python3-protobuf \
    python3-yaml \
    uuid-dev \
    vim \
    xmlto

RUN set -e \
    && cd /tmp \
    && git clone -b ${CRIU_BRANCH} ${CRIU_REPO} \
    && mv criu criu-build \
    && cd criu-build && \
    if [ "x${CRIU_SHA}" != x ] ; then \
        git reset --hard ${CRIU_SHA}; \
    fi; \
    make DESTDIR=/tmp/criu PREFIX=/usr/local LIBDIR=/usr/local/lib64 install-lib install-criu \
    && cd ../ \
    && tar -czf criu.tar.gz criu \
    && sha256sum criu.tar.gz > criu.tar.gz.sha256.txt \
    && rm -rf criu-build
