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

# fix dependencies
RUN ln -s /usr/lib/x86_64-linux-gnu/libicui18n.so.55 /usr/lib/x86_64-linux-gnu/libicui18n.so.56 && \
    ln -s /usr/lib/x86_64-linux-gnu/libicuuc.so.55 /usr/lib/x86_64-linux-gnu/libicuuc.so.56 && \
    ln -s /usr/lib/x86_64-linux-gnu/libicudata.so.55 /usr/lib/x86_64-linux-gnu/libicudata.so.56

# Install scrapy
RUN conda install -y -c conda-forge scrapy

##############################################################################
# settings
##############################################################################
RUN mkdir /app
VOLUME ["/app"]
WORKDIR /app