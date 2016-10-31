# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
FROM python-datascience

MAINTAINER Luca Minello <luca.minello@gmail.com>

USER root

# Spark 
ENV APACHE_SPARK_VERSION 2.0.1
ADD http://d3kbcqa49mib13.cloudfront.net/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz /tmp
RUN tar xzf /tmp/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz -C /usr/local && \
        rm /tmp/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7.tgz
RUN cd /usr/local && ln -s spark-${APACHE_SPARK_VERSION}-bin-hadoop2.7 spark

# Mesos dependencies
# Currently, Mesos is not available from Debian Jessie.
# So, we are installing it from Debian Wheezy. Once it
# becomes available for Debian Jessie. We should switch
# over to using that instead.
RUN DISTRO=debian && \
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



