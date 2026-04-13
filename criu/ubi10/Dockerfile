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

FROM registry.access.redhat.com/ubi10/ubi:latest

ARG CRIU_REPO=https://github.com/ibmruntimes/criu.git
ARG CRIU_BRANCH=criu-dev
# Last stable commit SHA
ARG CRIU_SHA

RUN dnf install -y yum-utils https://dl.fedoraproject.org/pub/epel/epel-release-latest-10.noarch.rpm \
    && yum-config-manager --enable codeready-builder-for-rhel-10-$(uname -m)-rpms*

# CRIU package dependencies
RUN yum -y update && yum install -y \
    asciidoc \
    automake \
    gcc \
    gcc-c++ \
    git \
    glibc \
    glibc-locale-source \
    gnutls-devel \
    iptables-libs \
    jansson \
    kernel-devel \
    libcap \
    libcap-devel \
    libibverbs \
    libmnl \
    libnet \
    libnet-devel \
    libnftnl \
    libnl3-devel \
    libpcap \
    libuuid-devel \
    make \
    nftables \
    nftables-devel \
    pkgconfig \
    protobuf-c \
    protobuf-c-devel \
    protobuf-devel \
    python3 \
    python3-pip \
    python3-protobuf \
    python3-setuptools \
    python3-wheel \
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
