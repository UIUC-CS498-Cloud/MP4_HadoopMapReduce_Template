# Fetch ubuntu 22.04 LTS docker image
FROM ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive

# Update apt, and install Java + curl + wget on your ubuntu image.
RUN apt-get update && \
    apt-get install -y \
    curl \
    vim \
    wget \
    expect \
    git \
    zip \
    unzip \
    openjdk-21-jdk \
    python3 \
    && apt-get clean

RUN curl -s "https://archive.apache.org/dist/hadoop/core/hadoop-3.3.6/hadoop-3.3.6.tar.gz" | tar -xz -C /usr/local/ \
    && ln -s /usr/local/hadoop-3.3.6 /usr/local/hadoop \
    && chmod a+rwx -R /usr/local/hadoop/
RUN wget https://repo1.maven.org/maven2/org/json/json/20240205/json-20240205.jar
RUN mkdir json-lib
RUN mv json-*.jar json-lib 
ENV HADOOP_PREFIX /usr/local/hadoop
ENV HADOOP_COMMON_HOME /usr/local/hadoop
ENV HADOOP_HDFS_HOME /usr/local/hadoop
ENV HADOOP_MAPRED_HOME /usr/local/hadoop
ENV HADOOP_YARN_HOME /usr/local/hadoop
ENV HADOOP_CONF_DIR /usr/local/hadoop/etc/hadoop
ENV YARN_CONF_DIR $HADOOP_PREFIX/etc/hadoop
ENV HADOOP_CLASSPATH $JAVA_HOME/lib/tools.jar:/json-lib/*.jar
ENV PATH="/usr/local/hadoop/bin:${PATH}"

# Test the arch and set JAVA_HOME accordingly:
# ARM64/aarch64: /usr/lib/jvm/java-8-openjdk-arm64
# X84_64: /usr/lib/jvm/java-1.8.0-openjdk-amd64

RUN if [ "$(uname -m)" = "x86_64" ]; then export JAVA_HOME=/usr/lib/jvm/java-1.21.0-openjdk-amd64; else export JAVA_HOME=/usr/lib/jvm/java-21-openjdk-arm64; fi \
    && export HADOOP_CLASSPATH=$JAVA_HOME/lib/tools.jar \
    && sed -i "/^export JAVA_HOME/ s:.*:export HADOOP_PREFIX=/usr/local/hadoop HADOOP_HOME=/usr/local/hadoop\n:" $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh \
    && sed -i "/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:" $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh \
    && echo "export JAVA_HOME=$JAVA_HOME" >> $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh \
    && echo "export JAVA_HOME=$JAVA_HOME" >> /root/.bashrc


# RUN sed -i "/^export JAVA_HOME/ s:.*:export HADOOP_PREFIX=/usr/local/hadoop HADOOP_HOME=/usr/local/hadoop\n:" $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh
# RUN sed -i '/^export HADOOP_CONF_DIR/ s:.*:export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop/:' $HADOOP_PREFIX/etc/hadoop/hadoop-env.sh


#COPY autosu /usr/local/bin
#RUN chmod 777 /usr/local/bin/autosu
#RUN adduser hadoopuser --disabled-password --gecos ""
#RUN echo 'hadoopuser:hadooppass' | chpasswd

# Make vim nice
RUN echo "set background=dark" >> ~/.vimrc
ENV PYSPARK_PYTHON=python3

COPY WordCount.java /etc/apt/WordCount.java
COPY TopWords.java /etc/apt/TopWords.java

# [Optional] Set working path to /cs498, and run the following command to start the container with code:
# WORKDIR /cs498
# docker run -it --rm --mount type=bind,source=$PATH_TO_CODE,target=/cs498/ sample_image.v1 /bin/bash