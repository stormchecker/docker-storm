# Dockerfile installing all dependencies for Storm
##################################################
# The Docker image can be built by executing:
# docker build -t yourusername/storm-dependencies .
# A different base image can be set from the commandline with:
# --build-arg BASE_IMAGE=<new_base_image>

# Set base image
ARG BASE_IMAGE=movesrwth/storm-basesystem:latest
FROM $BASE_IMAGE
ARG TARGETPLATFORM
LABEL org.opencontainers.image.authors="dev@stormchecker.org"

# Specify configurations
# These configurations can be set from the commandline with:
# --build-arg <config_name>=<value>

# Specify number of threads to use for parallel compilation
ARG no_threads=2

# Specify library versions
ARG CVC5_VERSION=1.4.2
ARG GUROBI_VERSION=13.0.2
ARG GUROBI_SHORT_VERSION=13.0
ARG HIGHS_VERSION=v1.15.1
ARG MATHSAT_VERSION=5.6.17
ARG SOPLEX_VERSION=8.0.3
ARG SPOT_VERSION=2.15.1


# Install additional packages
#############################
RUN apt-get -y update \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        curl \
        gnupg2 \
        python3 \
        python3-venv \
        wget \
    && update-ca-certificates


# Install cvc5
##############
# The build type has to be 'unrestricted': the default ('safe') and 'stable' reject some terms Storm creates (e.g. power).
# The cvc5 build creates a Python virtual environment (python3-venv, installed above) and installs its own build
# requirements into it with pip.
WORKDIR /opt/
RUN git clone --depth 1 https://github.com/cvc5/cvc5 -b cvc5-${CVC5_VERSION} \
    && cd cvc5 \
    && ./configure.sh unrestricted --auto-download --poly --prefix=/usr/local \
    && cd build \
    && make -j $no_threads \
    && make install \
    && rm -r /opt/cvc5


# Install Gurobi
#################
# From https://github.com/Gurobi/docker-optimizer/blob/master/12.0.2/Dockerfile
WORKDIR /opt
RUN if [ "$TARGETPLATFORM" = "linux/arm64" ]; then \
        export GRB_PLATFORM="armlinux64"; \
    else \
        export GRB_PLATFORM="linux64"; \
    fi \
    && wget -v https://packages.gurobi.com/${GUROBI_SHORT_VERSION}/gurobi${GUROBI_VERSION}_$GRB_PLATFORM.tar.gz \
    && tar -xvf gurobi${GUROBI_VERSION}_$GRB_PLATFORM.tar.gz  \
    && rm *.tar.gz \
    && mv -f gurobi* gurobi \
    && mv -f gurobi/$GRB_PLATFORM* gurobi/linux \
    && rm -rf gurobi/linux/docs

ENV GUROBI_HOME=/opt/gurobi/linux
ENV PATH=$PATH:$GUROBI_HOME/bin
ENV LD_LIBRARY_PATH=$GUROBI_HOME/lib


# Install HiGHS
###############
WORKDIR /opt/
RUN git clone https://github.com/ERGO-Code/HiGHS -b ${HIGHS_VERSION} \
    && mkdir HiGHS/build \
    && cd HiGHS/build \
    && cmake .. \
    && make -j $no_threads \
    && make install -j $no_threads \
    && rm -r /opt/HiGHS


# Install MathSAT
#################
WORKDIR /opt/
RUN if [ "$TARGETPLATFORM" = "linux/arm64" ]; then \
        export MATHSAT_PLATFORM="aarch64"; \
    else \
        export MATHSAT_PLATFORM="x86_64"; \
    fi \
    && curl -L -o mathsat5.tar.gz "https://mathsat.fbk.eu/release/mathsat-${MATHSAT_VERSION}-linux-${MATHSAT_PLATFORM}.tar.gz" \
    && tar -xzf mathsat5.tar.gz \
    && mv mathsat-${MATHSAT_VERSION}-linux-${MATHSAT_PLATFORM} mathsat5 \
    && rm mathsat5.tar.gz
ENV MATHSAT_ROOT=/opt/mathsat5


# Install SoPlex
################
WORKDIR /opt/
RUN curl -L -o soplex.tar.gz https://github.com/scipopt/soplex/archive/refs/tags/v${SOPLEX_VERSION}.tar.gz \
    && tar -xzf soplex.tar.gz \
    && rm soplex.tar.gz \
    && mv soplex-${SOPLEX_VERSION} soplex \
    && mkdir soplex/build \
    && cd soplex/build \
    && cmake .. \
    && make -j $no_threads \
    && make install -j $no_threads \
    && rm -r /opt/soplex


# Install Spot
##############
# Manual installation
WORKDIR /opt/
RUN curl -L -o spot.tar.gz http://www.lrde.epita.fr/dload/spot/spot-${SPOT_VERSION}.tar.gz \
    && tar -xzf spot.tar.gz \
    && rm spot.tar.gz \
    && cd spot-${SPOT_VERSION} \
    && ./configure --disable-python \
    && make -j $no_threads \
    && make install -j $no_threads \
    && cd /opt \
    && rm -r spot-${SPOT_VERSION}


