# Use Ubuntu 22.04 as the base image for x86 architecture
# Using a specific version of ubuntu is better for reproducibility
FROM --platform=linux/amd64 ubuntu:22.04 AS builder

# Set environment variables to avoid interactive prompts during installations
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=America/New_York

# Create a working directory
WORKDIR /opt/tools

# Install build dependencies
RUN --mount=type=cache,target=/var/cache/apt \
    --mount=type=cache,target=/var/lib/apt \
    apt-get update && apt-get install -y --no-install-recommends \
        build-essential \
        cmake \
        git \
        wget \
        curl \
        zlib1g-dev \
        libbz2-dev \
        liblzma-dev \
        libcurl4-openssl-dev \
        libssl-dev \
        libncurses5-dev \
        libncursesw5-dev \
        autoconf \
        automake \
        libtool \
        pkg-config \
        libisal-dev \
        libdeflate-dev \
        libgsl-dev \
        meson \
        libjsoncpp-dev \
        libpcre2-dev \
        && apt-get clean && rm -rf /var/lib/apt/lists/*

# Install samtools 1.18 from source
RUN wget --no-check-certificate https://github.com/samtools/samtools/releases/download/1.18/samtools-1.18.tar.bz2 && \
    tar -xjf samtools-1.18.tar.bz2 && \
    cd samtools-1.18 && \
    ./configure --prefix=/usr/local && \
    make && \
    make install

# Install R and required libraries
RUN --mount=type=cache,target=/var/cache/apt \
    --mount=type=cache,target=/var/lib/apt \
    apt-get update && \
    apt-get install -y --no-install-recommends software-properties-common dirmngr && \
    wget -qO- https://cloud.r-project.org/bin/linux/ubuntu/marutter_pubkey.asc | tee -a /etc/apt/trusted.gpg.d/cran_ubuntu_key.asc && \
    add-apt-repository "deb https://cloud.r-project.org/bin/linux/ubuntu $(lsb_release -cs)-cran40/" && \
    apt-get install -y --no-install-recommends r-base libgit2-dev libcurl4-openssl-dev libssl-dev && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# Install R packages
RUN R -e "install.packages(c('dplyr','ggplot2'), repos='http://cran.us.r-project.org')"

ENTRYPOINT ["/bin/bash", "-c"]

