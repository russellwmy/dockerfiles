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
WORKDIR /notebook

CMD jupyter notebook --no-browser --ip="0.0.0.0" --port=8888 --notebook-dir="/notebook"