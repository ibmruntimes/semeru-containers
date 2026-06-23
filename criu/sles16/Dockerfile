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

####################################################################################################################################################################################################
# docker build --build-arg FTP3_USER=<ftp3 username> --build-arg FTP3_PASS=<ftp3 password>  --build-arg CRIU_REPO=<repo> --build-arg CRIU_BRANCH=<branch> --build-arg CRIU_SHA=<sha> -f Dockerfile .
####################################################################################################################################################################################################

FROM registry.suse.com/bci/bci-base:16.0

WORKDIR /tmp

ARG CRIU_REPO=https://github.com/ibmruntimes/criu.git
ARG CRIU_BRANCH=criu-dev
# Last stable commit SHA
ARG CRIU_SHA

ARG FTP3_USER

# ftp won't work unless existing packages are updated
RUN --mount=type=secret,id=FTP3PASS \
    FTP3_PASSWORD="$(cat /run/secrets/FTP3PASS)" \
    && zypper update -y \
    && zypper ar -f ftp://${FTP3_USER}:${FTP3_PASSWORD}@ftp3install.linux.ibm.com/suse/scc/Backports/SLE-16.0_$(arch)/standard/ "SLES16.0 Standard"

RUN zypper --gpg-auto-import-keys refresh && \
    zypper install -y \
    asciidoc \
    automake \
    gcc \
    git \
    glibc \
    hostname \
    iproute2 \
    kernel-devel \
    libaio-devel \
    libbsd-devel \
    libcap2 \
    libcap-devel \
    libcap-ng-devel \
    libcap-progs \
    libgnutls30 \
    libgnutls-devel \
    libibverbs \
    libjansson4 \
    libmnl0 \
    libnet9 \
    libnet-devel \
    libnftables1 \
    libnftnl11 \
    libnl3-devel \
    libprotobuf-c1 \
    libuuid-devel \
    make \
    nftables \
    nftables-devel \
    pkg-config \
    protobuf-c-devel \
    protobuf-devel \
    python313 \
    python313-nftables \
    python313-pip \
    python313-protobuf \
    python313-PyYAML \
    python313-requests-futures \
    vim \
    xmlto

RUN set -e \
    && cd /tmp \
    && python3 -m pip install --upgrade pip setuptools \
    && git clone -b ${CRIU_BRANCH} ${CRIU_REPO} \
    && mv criu criu-build \
    && cd criu-build && \
    if [ "x${CRIU_SHA}" != x ] ; then \
        git reset --hard ${CRIU_SHA}; \
    fi; \
    make DESTDIR=/tmp/criu PREFIX=/usr/local LIBDIR=/usr/local/lib64 install-lib install-criu \
    && cd ../ \
    && rm -rf /tmp/criu/usr/local/lib/python* \
    && tar -czf criu.tar.gz criu \
    && sha256sum criu.tar.gz > criu.tar.gz.sha256.txt \
    && rm -rf criu-build
