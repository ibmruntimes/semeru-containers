# (C) Copyright IBM Corporation 2023, 2023
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

FROM registry.suse.com/suse/sle15:15.7

ARG CRIU_REPO=https://github.com/ibmruntimes/criu.git
ARG CRIU_BRANCH=upstream-master
# Last stable commit SHA
ARG CRIU_SHA

ARG FTP3_USER

RUN --mount=type=secret,id=FTP3PASS \
    FTP3_PASSWORD="$(cat /run/secrets/FTP3PASS)" \
    && zypper ar -f ftp://${FTP3_USER}:${FTP3_PASSWORD}@ftp3install.linux.ibm.com/suse/scc/Updates/SLE-Module-Python3/15-SP7/$(arch)/update "SLES15-SP7 Updates Python 3"

RUN zypper update -y && \
    zypper install -y \
    asciidoc \
    automake \
    gcc13 \
    git \
    glibc \
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
    libprotobuf-c-devel \
    libuuid-devel \
    make \
    nftables \
    nftables-devel \
    pkg-config \
    protobuf-devel \
    python311 \
    python311-future \
    python311-nftables \
    python311-pip \
    python311-protobuf \
    python311-PyYAML \
    vim \
    xmlto

RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.11 100 \
    && update-alternatives --set python3 /usr/bin/python3.11

RUN set -e \
    && cd /tmp \
    && git clone -b ${CRIU_BRANCH} ${CRIU_REPO} \
    && mv criu criu-build \
    && cd criu-build && \
    if [ "x${CRIU_SHA}" != x ] ; then \
        git reset --hard ${CRIU_SHA}; \
    fi; \
    && make CC=/usr/bin/gcc-13 DESTDIR=/tmp/criu PREFIX=/usr/local LIBDIR=/usr/local/lib64 install-lib install-criu \
    && cd ../ \
    && tar -czf criu.tar.gz criu \
    && sha256sum criu.tar.gz > criu.tar.gz.sha256.txt \
    && rm -rf criu-build
