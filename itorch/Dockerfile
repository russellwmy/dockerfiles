FROM buildpack-deps:jessie-scm

MAINTAINER Russell Wong <russellwmy@gmail.com>

##############################################################################
# OS Setup
##############################################################################
# Setup Unicode
RUN apt-get update && apt-get install -y --no-install-recommends\
    locales \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG en_US.utf8

# Install Anaconda dependencies
RUN apt-get update && apt-get install -y --no-install-recommends\
    bzip2 \
    libglib2.0-0 \
    libxext6 \
    libsm6 \
    libxrender1 \
    && rm -rf /var/lib/apt/lists/*

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
    ./install.sh \
    && rm -rf /var/lib/apt/lists/*

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