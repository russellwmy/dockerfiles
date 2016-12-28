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

##############################################################################
# anaconda python
##############################################################################
# Install Anaconda
ENV CONDA_DOWNLOAD_SCRIPT Anaconda3-4.2.0-Linux-x86_64.sh
ENV CONDA_DIR /opt/conda

RUN wget -q https://repo.continuum.io/archive/$CONDA_DOWNLOAD_SCRIPT && \
    bash $CONDA_DOWNLOAD_SCRIPT -b -p $CONDA_DIR && \
    rm $CONDA_DOWNLOAD_SCRIPT

ENV PATH $CONDA_DIR/bin:$PATH
RUN pip install --upgrade pip

# for jupyter notebook
EXPOSE 8888

##############################################################################
# settings
##############################################################################
RUN mkdir /notebook
VOLUME ["/notebook"]

ENTRYPOINT jupyter notebook --no-browser --ip="0.0.0.0" --port=8888 --notebook-dir="/notebook"
WORKDIR /notebook