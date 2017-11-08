FROM hub.c.163.com/public/ubuntu:14.04
MAINTAINER lixy <lixyon@aliyun.com>

ARG HBASE_VERSION=1.3.1

WORKDIR /

# Default to UTF-8 file.encoding
ENV LANG C.UTF-8

RUN apt-get update
RUN apt-get install -y openjdk-7-jdk wget tar
RUN rm -rf /var/lib/apt/lists/*
ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64
ENV PATH $PATH:$JAVA_HOME/bin
ENV PATH $PATH:/hbase/bin

ENV url http://www.apache.org/dyn/closer.lua?filename=hbase/1.3.1/hbase-1.3.1-bin.tar.gz&action=download
ENV url_archive="http://archive.apache.org/dist/hbase/1.3.1/hbase-1.3.1-bin.tar.gz"

RUN wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-1.3.1-bin.tar.gz" "$url" || \
    wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-1.3.1-bin.tar.gz" "$url_archive" && \
    mkdir "hbase-1.3.1" && \
    tar zxf "hbase-1.3.1-bin.tar.gz" -C "hbase-1.3.1" --strip 1 && \
    test -d "hbase-1.3.1" && \
    ln -sv "hbase-1.3.1" hbase && \
    rm -fv "hbase-1.3.1-bin.tar.gz" && \
    { rm -rf hbase/{docs,src}; : ; }

ADD conf/hbase-site.xml /hbase/conf/
ADD conf/hbase-env.sh /hbase/conf/
ADD conf/regionservers /hbase/conf/

# Stargate  8080  / 8085
# Thrift    9090  / 9095
# HMaster   16000 / 16010
# RS        16201 / 16301
EXPOSE 2181 8080 8085 9090 9095 16000 16010 16201 16301

CMD "/hbase/bin/start-hbase.sh"
