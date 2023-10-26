##############################################
# Created from template ros2.dockerfile.jinja
##############################################

###########################################
# Base image
###########################################
FROM quay.io/jupyter/datascience-notebook:latest AS base

USER root

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get -y upgrade \
    && rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=

###########################################
#  Develop image
###########################################
FROM base AS dev

ENV DEBIAN_FRONTEND=noninteractive

# Add sudo support for the non-root user
RUN apt-get update \
  && apt-get install -y sudo \
  && echo $NB_USER ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$NB_USER\
  && chmod 0440 /etc/sudoers.d/$NB_USER \
  && rm -rf /var/lib/apt/lists/*

ENV DEBIAN_FRONTEND=
ENV AMENT_CPPCHECK_ALLOW_SLOW_VERSIONS=1

###########################################
#  Full image
###########################################
FROM dev AS full

ENV DEBIAN_FRONTEND=noninteractive

# Install Pluto.jl
RUN julia -e 'using Pkg; Pkg.update(); Pkg.add(["Pluto", "PlutoUI", "Plots"]); Pkg.precompile();'

# Install RASPA
RUN apt-get update && apt-get install -y --no-install-recommends \
  autotools-dev \
  autoconf \
  libtool \
  automake \
  && rm -rf /var/lib/apt/lists/*
RUN wget https://github.com/iRASPA/RASPA2/archive/refs/tags/v2.0.47.zip && \
    unzip v2.0.47.zip && \
    cd RASPA2-2.0.47 && \
    rm -rf autom4te.cache && \
    mkdir m4 && \
    aclocal && \
    autoreconf -i && \
    automake --add-missing && \
    autoconf && \
    ./scripts/CompileScript/make-gcc-local && \
    make && \
    make install && \
    cd .. && \
    rm v2.0.47.zip && \
    rm -r RASPA2-2.0.47
ENV RASPA_DIR=${HOME}/RASPA/simulations

ENV DEBIAN_FRONTEND=

# Set up auto-source of workspace for ros user
ARG WORKSPACE