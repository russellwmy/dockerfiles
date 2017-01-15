FROM russellwmy/golang

MAINTAINER Russell Wong <russellwmy@gmail.com>

##############################################################################
# Java Setup
##############################################################################
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    echo "deb http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | tee /etc/apt/sources.list.d/webupd8team-java.list && \
    echo "deb-src http://ppa.launchpad.net/webupd8team/java/ubuntu xenial main" | tee -a /etc/apt/sources.list.d/webupd8team-java.list && \
    apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys EEA14886 &&\
    apt-get update && \
    echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 select true" | debconf-set-selections && \
    echo "oracle-java8-installer shared/accepted-oracle-license-v1-1 seen true" | debconf-set-selections && \
    apt-get install -y oracle-java8-installer

RUN echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list && \
    curl https://bazel.build/bazel-release.pub.gpg | apt-key add -

##############################################################################
# Bazel Setup
##############################################################################
RUN apt-get update && \
    apt-get install -y bazel && \
    apt-get upgrade -y bazel


##############################################################################
# Tesnorflow Setup
##############################################################################
RUN apt-get install -y python swig python-numpy libcurl3-dev && \
    go get -d -v github.com/tensorflow/tensorflow/tensorflow/go &&\
    cd $GOPATH/src/github.com/tensorflow/tensorflow  && \
    # change to GPU if you want to do so
    tensorflow/tools/ci_build/builds/configured CPU 


##############################################################################
# Tensorflow Go Setup
##############################################################################
RUN cd $GOPATH/src/github.com/tensorflow/tensorflow && \
    bazel build -c opt //tensorflow:libtensorflow.so && \
    cp $GOPATH/src/github.com/tensorflow/tensorflow/bazel-bin/tensorflow/libtensorflow.so /usr/lib && \
    go generate github.com/tensorflow/tensorflow/tensorflow/go/op

##############################################################################
# Clean
##############################################################################
RUN rm -rf /var/lib/apt/lists/*
