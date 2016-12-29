FROM ubuntu:latest

MAINTAINER Russell Wong <russellwmy@gmail.com>

# locale
RUN cp /usr/share/zoneinfo/Asia/Hong_Kong /etc/localtime
RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# Install Anaconda dependencies
RUN apt-get update && apt-get install -y \
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
    && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Install git and iTorch dependencies
RUN apt-get update && apt-get install -y \
    software-properties-common \
    libzmq3-dev \
    libssl-dev \
    python-zmq

##############################################################################
# anaconda python
##############################################################################
# Install miniconda
ENV CONDA_DOWNLOAD_SCRIPT Miniconda3-latest-Linux-x86_64.sh
ENV CONDA_DIR /opt/conda

RUN wget -q https://repo.continuum.io/miniconda/$CONDA_DOWNLOAD_SCRIPT && \
    bash $CONDA_DOWNLOAD_SCRIPT -b -p $CONDA_DIR && \
    rm $CONDA_DOWNLOAD_SCRIPT

ENV PATH $CONDA_DIR/bin:$PATH

# Install jupyter
RUN conda install -y jupyter
EXPOSE 8888

##############################################################################
# Torch
##############################################################################
# Torch7 installation scripts - http://torch.ch/docs/getting-started.html
RUN cd /opt && \
    git clone https://github.com/torch/distro.git torch --recursive

ADD install-deps /opt/torch/install-deps

RUN cd /opt/torch && \
    bash install-deps && \
    ./install.sh

ENV PATH=/opt/torch/install/bin:$PATH

# Install iTorch
RUN git clone https://github.com/facebook/iTorch.git && \
    cd iTorch && \
    luarocks make

##############################################################################
# settings
##############################################################################
RUN mkdir /notebook
VOLUME ["/notebook"]
WORKDIR /notebook

CMD itorch notebook --no-browser --ip="0.0.0.0" --port=8888 --notebook-dir="/notebook"