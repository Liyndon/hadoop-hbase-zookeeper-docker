FROM hub.c.163.com/public/centos:6.5
MAINTAINER lixy <lixyon@163.com>

USER root

# install dev tools
# RUN apt-get update
# curl tar sudo openssh-server openssh-client rsync
RUN yum install -y openssh-server

# passwordless ssh
RUN rm -rf /etc/ssh/ssh_host_dsa_key \
    rm -rf /etc/ssh/ssh_host_rsa_key \
    rm -rf /root/.ssh/id_rsa \
    ssh-keygen -q -N "" -t dsa -f /etc/ssh/ssh_host_dsa_key \
    ssh-keygen -q -N "" -t rsa -f /etc/ssh/ssh_host_rsa_key \
    ssh-keygen -q -N "" -t rsa -f /root/.ssh/id_rsa \
    cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys 

# java
RUN mkdir -p /usr/local/java && \
     curl -Ls 'http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz' -H 'Cookie: oraclelicense=accept-securebackup-cookie' | \
     tar --strip-components=1 -xz -C /usr/local/java/

ENV JAVA_HOME /usr/local/java
ENV PATH $PATH:$JAVA_HOME/bin

# download native support
RUN mkdir -p /tmp/native \
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

ENV HADOOP_HOME /usr/local/hadoop \
    HADOOP_CONF_DIR /usr/local/hadoop/etc/hadoop \
    HADOOP_LOG_DIR /usr/local/hadoop/logs

RUN sed -i '/^export JAVA_HOME/ s:.*:export JAVA_HOME=/usr/local/java\nexport H=/usr/local/hadoop\nexport HADOOP_HOME=/usr/local/hadoop\n:' $HADOOP_HOME/etc/hadoop/hadoop-env.sh \
    sed -i '/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:' $HADOOP_HOME/etc/hadoop/hadoop-env.sh
  
ADD hadoop/conf/core-site.xml $HADOOP_HOME/etc/hadoop/core-site.xml \
    hadoop/conf/hdfs-site.xml $HADOOP_HOME/etc/hadoop/hdfs-site.xml \
    hadoop/conf/mapred-site.xml $HADOOP_HOME/etc/hadoop/mapred-site.xml \
    hadoop/conf/yarn-site.xml $HADOOP_HOME/etc/hadoop/yarn-site.xml \
    hadoop/conf/slave $HADOOP_HOME/etc/hadoop/slave

# init hdfs
RUN $HADOOP_HOME/bin/hdfs namenode -format

ADD ssh_config /root/.ssh/config 
RUN chmod 600 /root/.ssh/config \
    chown root:root /root/.ssh/config

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
CMD ["$HADOOP_HOME/sbin/start-all.sh"]
