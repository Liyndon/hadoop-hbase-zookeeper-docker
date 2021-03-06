FROM hub.c.163.com/public/ubuntu:14.04
MAINTAINER lixy <lixyon@163.com>

USER root

# install dev tools

# 更新软件源
RUN apt-get update
RUN apt-get install -y curl openjdk-7-jdk tar sudo openssh-server openssh-client rsync

# passwordless ssh
RUN rm -f /etc/ssh/ssh_host_dsa_key /etc/ssh/ssh_host_rsa_key /root/.ssh/id_rsa
RUN ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key
RUN ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key
RUN ssh-keygen -q -N "" -t rsa -f /root/.ssh/id_rsa
RUN cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys

ENV JAVA_HOME /usr/lib/jvm/java-7-openjdk-amd64
ENV PATH $PATH:$JAVA_HOME/bin

# download native support
RUN mkdir -p /tmp/native && \
    curl -Ls http://dl.bintray.com/sequenceiq/sequenceiq-bin/hadoop-native-64-2.7.0.tar \
    | tar -x -C /tmp/native

ENV HADOOP_VERSION=2.6.5
RUN curl -s http://www.eu.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz | tar -xz -C /usr/local/
WORKDIR /usr/local
RUN ln -s /usr/local/hadoop-${HADOOP_VERSION} /usr/local/hadoop && \
    rm -rf /usr/local/hadoop/lib/native && \
    mv /tmp/native /usr/local/hadoop/lib && \
    mkdir -p /root/hadoop/name && \
    mkdir -p /root/hadoop/data

ENV HADOOP_HOME /usr/local/hadoop

ADD conf/core-site.xml /usr/local/hadoop/etc/hadoop/core-site.xml \
    conf/hdfs-site.xml /usr/local/hadoop/etc/hadoop/hdfs-site.xml \
   conf/mapred-site.xml /usr/local/hadoop/etc/hadoop/mapred-site.xml \
    conf/yarn-site.xml /usr/local/hadoop/etc/hadoop/yarn-site.xml \
    conf/slave /usr/local/hadoop/etc/hadoop/slave \
    conf/hadoop-env.sh /usr/local/hadoop/etc/hadoop/hadoop-env.sh

# init hdfs
RUN /usr/local/hadoop/bin/hdfs namenode -format

#ADD ssh_config /root/.ssh/config 
#RUN chmod 600 /root/.ssh/config \
#    chown root:root /root/.ssh/config

RUN service ssh start

# Hdfs ports
EXPOSE 9000 50010 50020 50070 50075 50090
# See https://issues.apache.org/jira/browse/HDFS-9427
EXPOSE 9871 9870 9820 9869 9868 9867 9866 9865 9864
# Mapred ports
EXPOSE 19888
#Yarn ports
EXPOSE 8030 8031 8032 8033 8040 8042 8088 8188
#Other ports
EXPOSE 49707 2122
