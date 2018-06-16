#
# This Docker image encapsulates Radare2, the reverse-engineering framework. 
# Radare2 is available at http://radare.org
#
# Replace "~/workspace" with the path to your working directory on the host:
#
# sudo docker run --rm -it -v ~/workspace:/home/nonroot/workspace kess/radare2
#
# Then run "r2" or other Radare2 commands inside the container.
#
# Before running the application,
# create ~/workspace on your host and make it world-accessible ("chmod a+xwr").
#
# This Dockerfile is based on the official Radare2 Dockerfile file at
# https://github.com/radare/radare2/blob/master/Dockerfile
#


#Using Ubuntu 16.04 as base image.
FROM ubuntu:16.04

# Radare version
ENV R2_VERSION master

# R2pipe python version
ENV R2_PIPE_PY_VERSION 0.8.9

# R2pipe node version
ENV R2_PIPE_NPM_VERSION 2.3.2

# Build radare2 in a volume to minimize space used by build
VOLUME ["/mnt"]

# Install all build dependencies & bindings
# Build and install radare2 on master branch
# Remove all build dependencies & Cleanup
RUN DEBIAN_FRONTEND=noninteractive dpkg --add-architecture i386 && \
  apt-get update && \
  apt-get install -y \
  vim \
  curl \
  wget \
  gcc \
  git \
  bison \
  pkg-config \
  make \
  glib-2.0 \
  libc6:i386 \
  libncurses5:i386 \
  libstdc++6:i386 \
  gnupg2 \
  sudo && \
  curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
  apt-get install -y nodejs python-pip && \
  pip install r2pipe=="$R2_PIPE_PY_VERSION" && \
  npm install --unsafe-perm -g "r2pipe@$R2_PIPE_NPM_VERSION" && \
  cd /mnt && \
  git clone -b "$R2_VERSION" --depth 1 https://github.com/radare/radare2.git && \
  cd radare2 && \
  ./sys/install.sh && \
  make install && \
  apt-get install -y xz-utils && \
  apt-get remove --purge -y \
  bison \
  python-pip \
  glib-2.0 && \
  apt-get autoremove --purge -y && \
  apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Create non-root user
RUN useradd -m nonroot && \
  adduser nonroot sudo && \
  echo "nonroot:nonroot" | chpasswd

# Initilise base user
USER nonroot
ENV HOME /home/nonroot

# Create working directory
RUN mkdir /home/nonroot/workspace
WORKDIR /home/nonroot/workdir

# Setup r2pm
RUN r2pm init && \
  r2pm update && \
  chown -R nonroot:nonroot /home/nonroot/.config

# Base command for container
CMD ["/bin/bash"]