# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# ORC compile for Debian 8
#

FROM debian:8
MAINTAINER Owen O'Malley <owen@hortonworks.com>

ADD sources.list apt.conf /etc/apt/

RUN apt-get update
RUN apt-get install -y \
  autoconf \
  cmake \
  gcc \
  g++ \
  git \
  libsasl2-dev \
  libssl-dev \
  libtool \
  make \
  maven \
  pkg-config \
  wget

RUN apt-get -t jessie-backports install -y openjdk-8-jdk
RUN update-alternatives --set java \
  /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

WORKDIR /root
# install a local build of snappy
RUN wget "https://github.com/google/snappy/archive/1.1.3.tar.gz" \
  -O snappy.tgz
RUN tar xzf snappy.tgz
RUN cd snappy-1.1.3 && \
    ./autogen.sh && \
    ./configure && \
    make install

VOLUME /root/.m2/repository

CMD if [ ! -d orc ]; then \
      echo "No volume provided, building from apache master."; \
      echo "Pass '-v`pwd`:/root/orc' to docker run to build local source."; \
      git clone https://github.com/apache/orc.git -b master; \
    fi && \
    mkdir build && \
    cd build && \
    cmake -DSNAPPY_HOME=/usr/local ../orc && \
    make package test-out
