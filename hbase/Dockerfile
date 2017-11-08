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

    # HBase 0.94
    if   [ "${HBASE_VERSION:0:4}" = "0.94" ]; then \
        url="http://www.apache.org/dyn/closer.lua?filename=hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION.tar.gz&action=download"; \
        url_archive="http://archive.apache.org/dist/hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION.tar.gz"; \
    # HBase 0.96 usually missing from mirrors, have to use apache archive
    elif [ "${HBASE_VERSION:0:4}" = "0.96" ]; then \
        url="https://archive.apache.org/dist/hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION-hadoop2-bin.tar.gz"; \
        url_archive="http://archive.apache.org/dist/hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION-hadoop2-bin.tar.gz"; \
    # HBase 0.98
    elif [ "${HBASE_VERSION:0:4}" = "0.98" ]; then \
        url="http://www.apache.org/dyn/closer.lua?filename=hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-hadoop2-bin.tar.gz&action=download"; \
        url_archive="http://archive.apache.org/dist/hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-hadoop2-bin.tar.gz"; \
    # HBase 1.0
    elif [ "${HBASE_VERSION:0:3}" = "1.0"  ]; then \
        url="http://www.apache.org/dyn/closer.lua?filename=hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz&action=download"; \
        url_archive="http://archive.apache.org/dist/hbase/hbase-$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz"; \
    # HBase 1.1+
    elif [ "${HBASE_VERSION:0:1}" = "1"    ]; then \
        url="http://www.apache.org/dyn/closer.lua?filename=hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz&action=download"; \
        url_archive="http://archive.apache.org/dist/hbase/$HBASE_VERSION/hbase-$HBASE_VERSION-bin.tar.gz"; \
    else \
        echo "unrecognized HBase version"; exit 1; \
    fi && \
    # --max-redirect - some apache mirrors redirect a couple times and give you the latest version instead
    #                  but this breaks stuff later because the link will not point to the right dir
    #                  (and is also the wrong version for the tag)

RUN wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-$HBASE_VERSION-bin.tar.gz" "$url" || \
    wget -t 10 --max-redirect 1 --retry-connrefused -O "hbase-$HBASE_VERSION-bin.tar.gz" "$url_archive" && \
    mkdir "hbase-$HBASE_VERSION" && \
    tar zxf "hbase-$HBASE_VERSION-bin.tar.gz" -C "hbase-$HBASE_VERSION" --strip 1 && \
    test -d "hbase-$HBASE_VERSION" && \
    ln -sv "hbase-$HBASE_VERSION" hbase && \
    rm -fv "hbase-$HBASE_VERSION-bin.tar.gz" && \
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
