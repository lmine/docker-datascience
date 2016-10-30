# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM python-datascience

MAINTAINER Luca Minello <luca.minello@gmail.com>

USER root

# # HADOOP
# ENV HADOOP_VERSION 2.7.2
# ENV HADOOP_HOME /usr/hadoop-$HADOOP_VERSION
# ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
# ENV PATH $PATH:$HADOOP_HOME/bin
# RUN curl -sL --retry 3 \
#   "http://archive.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz" \
#   | gunzip \
#   | tar -x -C /usr/ \
#  && rm -rf $HADOOP_HOME/share/doc \
#  && chown -R root:root $HADOOP_HOME

# # SPARK
# ENV SPARK_VERSION 2.0.1
# ENV SPARK_PACKAGE spark-${SPARK_VERSION}-bin-without-hadoop
# ENV SPARK_HOME /usr/spark-${SPARK_VERSION}
# ENV SPARK_DIST_CLASSPATH="$HADOOP_HOME/etc/hadoop/*:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*:$HADOOP_HOME/share/hadoop/tools/lib/*"
# ENV PATH $PATH:${SPARK_HOME}/bin
# RUN curl -sL --retry 3 \
#   "http://d3kbcqa49mib13.cloudfront.net/${SPARK_PACKAGE}.tgz" \
#   | gunzip \
#   | tar x -C /usr/ \
#  && mv /usr/$SPARK_PACKAGE $SPARK_HOME \
#  && chown -R root:root $SPARK_HOME

# Spark dependencies
ENV APACHE_SPARK_VERSION 2.0.1
# RUN apt-get -y update && \
#     apt-get install -y --no-install-recommends openjdk-7-jre-headless && \
#     apt-get clean && \
#     rm -rf /var/lib/apt/lists/*

ADD http://d3kbcqa49mib13.cloudfront.net/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz /tmp
RUN tar xzf /tmp/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz -C /usr/local && \
        rm /tmp/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz
RUN cd /usr/local && ln -s spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7 spark

# Mesos dependencies
# Currently, Mesos is not available from Debian Jessie.
# So, we are installing it from Debian Wheezy. Once it
# becomes available for Debian Jessie. We should switch
# over to using that instead.
RUN apt-get -y update && \
    apt-get --no-install-recommends -y install gnupg && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

#RUN apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF && \
RUN    DISTRO=debian && \
    CODENAME=wheezy && \
    echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" > /etc/apt/sources.list.d/mesosphere.list && \
    apt-get -y update && \
    apt-get --no-install-recommends -y --force-yes install mesos && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Spark and Mesos config
ENV SPARK_HOME /usr/local/spark
ENV PYTHONPATH $SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.10.3-src.zip
ENV MESOS_NATIVE_LIBRARY /usr/local/lib/libmesos.so
ENV SPARK_OPTS --driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info


USER $NB_USER

# Apache Toree kernel
RUN pip --no-cache-dir install toree
RUN jupyter toree install --user --spark_home=$SPARK_HOME



