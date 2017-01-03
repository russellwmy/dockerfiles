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

# Gcc for building packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    libopenblas-dev \
    libopencv-dev \
    && rm -rf /var/lib/apt/lists/*

##############################################################################
# MxNet Setup
##############################################################################
RUN git clone --recursive https://github.com/dmlc/mxnet/ && cd mxnet && \
    cp make/config.mk . && \
    echo "USE_BLAS=openblas" >>config.mk && \
    make -j$(nproc)

##############################################################################
# Anaconda Steup
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
# Julia Setup
##############################################################################
ENV JULIA_PATH /usr/local/julia
ENV JULIA_VERSION 0.5.0

RUN mkdir $JULIA_PATH \
  && apt-get update && apt-get install -y curl \
  && curl -sSL "https://julialang.s3.amazonaws.com/bin/linux/x64/${JULIA_VERSION%[.-]*}/julia-${JULIA_VERSION}-linux-x86_64.tar.gz" -o julia.tar.gz \
  && curl -sSL "https://julialang.s3.amazonaws.com/bin/linux/x64/${JULIA_VERSION%[.-]*}/julia-${JULIA_VERSION}-linux-x86_64.tar.gz.asc" -o julia.tar.gz.asc \
  && export GNUPGHOME="$(mktemp -d)" \
# http://julialang.org/juliareleases.asc
# Julia (Binary signing key) <buildbot@julialang.org>
  && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys 3673DF529D9049477F76B37566E3C7DC03D6E495 \
  && gpg --batch --verify julia.tar.gz.asc julia.tar.gz \
  && rm -r "$GNUPGHOME" julia.tar.gz.asc \
  && tar -xzf julia.tar.gz -C $JULIA_PATH --strip-components 1 \
  && rm -rf /var/lib/apt/lists/* julia.tar.gz*


ENV PATH $JULIA_PATH/bin:$PATH

RUN julia -e 'Pkg.add("IJulia")' && julia -e 'Pkg.add("Plots")' && julia -e 'Pkg.add("MXNet")'

##############################################################################
# settings
##############################################################################
RUN mkdir /notebook
VOLUME ["/notebook"]
WORKDIR /notebook

CMD jupyter notebook --no-browser --ip="0.0.0.0" --port=8888 --notebook-dir="/notebook"