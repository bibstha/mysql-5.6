FROM gcr.io/shopify-base-images/ubuntu/xenial:latest

# The ubuntu-toolchain-r/test is to get more recent versions of gdb
# These dependencies are required for building mysql and running tests
RUN apt-get update && \
    apt-get -fy install software-properties-common && \
    add-apt-repository -y ppa:ubuntu-toolchain-r/test && \
    apt-get update && \
    apt-get install -fy wget cmake g++ libncurses5-dev libreadline-dev \
      zlib1g-dev libcurl4-openssl-dev python python-pip unzip perl \
      gdb vim dh-make devscripts valgrind stress ruby ruby-dev bison ccache && \
      gem install rake package_cloud 

RUN apt-get update && \
    apt-get -fy install libaio-dev libmecab-dev psmisc libpam-dev \
                        libssl-dev libnuma-dev libwrap0-dev libldap2-dev \
                        dh-systemd libcurl4-openssl-dev libldap2-dev \
                        liblz4-dev git libboost-all-dev libcap-dev

RUN cd /tmp && \
    wget https://github.com/facebook/zstd/releases/download/v1.4.5/zstd-1.4.5.tar.gz && \
    tar -zxf zstd-1.4.5.tar.gz && \
    cd zstd-1.4.5 && make && make install

ENV CACHE_DIR=/ccache_cache_dir
# ccache will cache object files for faster compilation times.
# make just uses stat to look at modification times, this doesn't
# play well when we mount old caches
RUN /usr/sbin/update-ccache-symlinks && \
    # Set the cache directory so that we know where to mount
    ccache -o cache_dir=$CACHE_DIR && \ 
    # Set the maximum number of files and maximum size of cache to unlimited.
    ccache -F 0 && ccache -M 0  

ENV PATH=/usr/lib/ccache:$PATH

# Env var is required for MySQL-python
# Required for tests, LD_LIBRARY_PATH
ENV PATH="/usr/local/mysql/bin/:${PATH}"
ENV LD_LIBRARY_PATH="/usr/local/mysql/lib/:${LD_LIBRARY_PATH}"

RUN groupadd mysql && useradd -r -g mysql -s /bin/bash mysql

EXPOSE 3478
WORKDIR /mysql_build
