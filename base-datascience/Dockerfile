# Debian Jessie image released 2016 May 03.
FROM debian:stretch

MAINTAINER Luca Minello <luca.minello@gmail.com>

USER root

# Install all OS dependencies for notebook server that starts but lacks all
# features (e.g., download as all possible file formats)
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && apt-get -yq dist-upgrade \
    && apt-get install -yq --no-install-recommends \
    wget \
    bzip2 \
    ca-certificates \
    sudo \
    locales \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Install all OS dependencies for fully functional notebook server
RUN apt-get update && apt-get install -yq --no-install-recommends \
    git \
    nano \
    build-essential \
    python-dev \
    unzip \
    libsm6 \
    pandoc \
    libxrender1 \
    inkscape \
    bash-completion \
    bison \
    debhelper \
    default-jdk \
    g++ \
    gcc \
    gnupg \
    gdb \
    gfortran \
    groff-base \
    libblas-dev \
    libbz2-dev \
    libcairo2-dev \
    libcurl4-openssl-dev \
    libjpeg-dev \
    liblapack-dev \
    liblzma-dev \
    libncurses5-dev \
    libpango1.0-dev \
    libpcre3-dev \
    libpng-dev \
    libreadline-dev \
    libtiff5-dev \
    libx11-dev \
    libxt-dev \
    mpack \
    subversion \
    tcl8.6-dev \
    texinfo \
    texlive-base \
    texlive-extra-utils \
    texlive-fonts-extra \
    texlive-fonts-recommended \
    texlive-generic-recommended \
    texlive-latex-base \
    texlive-latex-extra \
    texlive-latex-recommended \
    tk8.6-dev \
    x11proto-core-dev \
    xauth \
    xdg-utils \
    xfonts-base \
    xvfb \
    zlib1g-dev \
    unixodbc-dev \
    ca-certificates \
    file \
    git \
    libapparmor1 \
    libedit2 \
    libcurl4-openssl-dev \
    libssl1.0.2 \
    libssl-dev \
    psmisc \
    python-setuptools \
    procps \
    fonts-dejavu \
    libav-tools \
    && apt-get clean && \
    rm -rf /var/lib/apt/lists/*

ADD http://security.debian.org/debian-security/pool/updates/main/o/openssl/libssl1.0.0_1.0.1t-1+deb8u5_amd64.deb /tmp
RUN dpkg -i /tmp/libssl1.0.0_1.0.1t-1+deb8u5_amd64.deb

RUN echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
   locale-gen

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8

#Use S6
ADD https://github.com/just-containers/s6-overlay/releases/download/v1.9.1.3/s6-overlay-amd64.tar.gz /tmp/
RUN tar xzf /tmp/s6-overlay-amd64.tar.gz -C /


# Configure environment
ENV CONDA_DIR /opt/conda
ENV PATH $CONDA_DIR/bin:$PATH
ENV SHELL /bin/bash
ENV NB_USER admin
ENV NB_PASSWORD admin
ENV NB_UID 1000
ENV HOME /home/$NB_USER
ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US.UTF-8

# Create NB_USER user with UID=1000 and in the 'users' group
RUN useradd -m -s /bin/bash -N -u $NB_UID $NB_USER && \
    mkdir -p $CONDA_DIR && \
    chown $NB_USER $CONDA_DIR && \
    echo "$NB_USER:$NB_PASSWORD" | chpasswd

RUN adduser $NB_USER sudo && echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER $NB_USER

# Setup NB_USER home directory
RUN mkdir /home/$NB_USER/work && \
    mkdir /home/$NB_USER/.jupyter && \
    echo "cacert=/etc/ssl/certs/ca-certificates.crt" > /home/$NB_USER/.curlrc

# Install conda as NB_USER
RUN cd /tmp && \
    mkdir -p $CONDA_DIR && \
    wget --quiet https://repo.continuum.io/miniconda/Miniconda3-4.1.11-Linux-x86_64.sh && \
    echo "efd6a9362fc6b4085f599a881d20e57de628da8c1a898c08ec82874f3bad41bf *Miniconda3-4.1.11-Linux-x86_64.sh" | sha256sum -c - && \
    /bin/bash Miniconda3-4.1.11-Linux-x86_64.sh -f -b -p $CONDA_DIR && \
    rm Miniconda3-4.1.11-Linux-x86_64.sh && \
    $CONDA_DIR/bin/conda install --quiet --yes conda==4.1.11 && \
    $CONDA_DIR/bin/conda config --system --add channels conda-forge && \
    $CONDA_DIR/bin/conda config --system --add channels anaconda-nb-extensions && \
    $CONDA_DIR/bin/conda config --system --set auto_update_conda false && \
    conda clean -tipsy

# Temporary workaround for https://github.com/jupyter/docker-stacks/issues/210
# Stick with jpeg 8 to avoid problems with R packages
RUN echo "jpeg 8*" >> /opt/conda/conda-meta/pinned

USER root

# Configure container startup
ENTRYPOINT ["/init"]


USER $NB_USER
