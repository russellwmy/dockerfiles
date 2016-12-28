FROM ubuntu:latest

MAINTAINER Russell Wong "russellwmy@gmail.com"

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8


# install apt packages
RUN apt-get update --fix-missing &&  apt-get install -y \
    curl \
    grep \
    sed \
    dpkg \
    wget \
    bzip2 \
    ca-certificates \
    libglib2.0-0 \
    libxext6 \
    libsm6 \
    libxrender1 \
    git \
    mercurial \
    subversion \
    build-essential \
    libatlas-base-dev \
    libboost-all-dev \
    libgflags-dev \
    libgoogle-glog-dev \
    libhdf5-serial-dev \
    libleveldb-dev \
    libsnappy-dev \
    liblmdb-dev && \
    rm -rf /var/lib/apt/lists/*

# fix the versioning issue of lib
RUN ln -s /usr/lib/x86_64-linux-gnu/libboost_python-py34.so.1.55.0 /usr/local/lib/libboost_python3.so &&\
    ln -s /usr/lib/x86_64-linux-gnu/libhdf5_serial.so.8.0.2 /usr/lib/x86_64-linux-gnu/libhdf5.so &&\
    ln -s /usr/lib/x86_64-linux-gnu/libhdf5_serial_hl.so.8.0.2 /usr/lib/x86_64-linux-gnu/libhdf5_hl.so &&\
    ldconfig

# install conda
RUN echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh && \
    wget --quiet https://repo.continuum.io/archive/Anaconda3-4.2.0-Linux-x86_64.sh -O ~/anaconda.sh && \
    /bin/bash ~/anaconda.sh -b -p /opt/conda && \
    rm ~/anaconda.sh

RUN TINI_VERSION=`curl https://github.com/krallin/tini/releases/latest | grep -o "/v.*\"" | sed 's:^..\(.*\).$:\1:'` && \
    curl -L "https://github.com/krallin/tini/releases/download/v${TINI_VERSION}/tini_${TINI_VERSION}.deb" > tini.deb && \
    dpkg -i tini.deb && \
    rm tini.deb && \
    apt-get clean

ENV PATH /opt/conda/bin:$PATH

RUN conda update conda &&\
    conda install -y libgcc

# install protobuf
RUN cd /opt && \
    mkdir protobuf &&\
    cd protobuf &&\
    wget https://github.com/google/protobuf/releases/download/v3.1.0/protobuf-cpp-3.1.0.tar.gz &&\
    tar xzf protobuf-cpp-3.1.0.tar.gz &&\
    cd protobuf-3.1.0 &&\
    ./configure &&\
    make &&\
    make install &&\
    cd .. &&\
    wget https://github.com/google/protobuf/releases/download/v3.1.0/protobuf-python-3.1.0.tar.gz &&\
    tar xzf protobuf-python-3.1.0.tar.gz  &&\
    cd protobuf-3.1.0/python &&\
    python3 setup.py build &&\
    python3 setup.py test &&\
    python3 setup.py install &&\
    ldconfig


RUN cd /opt && \
    git clone https://github.com/BVLC/caffe.git

ADD Makefile.config /opt/caffe/Makefile.config

RUN cd /opt/caffe &&\
    find . -type f -exec sed -i -e 's^"hdf5.h"^"hdf5/serial/hdf5.h"^g' -e 's^"hdf5_hl.h"^"hdf5/serial/hdf5_hl.h"^g' '{}' \; &&\
    make &&\
    make pycaffe

ENV PYTHONPATH /opt/caffe/python

EXPOSE 8888

WORKDIR /opt/workspace

CMD jupyter notebook --no-browser --ip="*"