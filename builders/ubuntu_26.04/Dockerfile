# Base our image from ubuntu Raccoon
FROM ubuntu:26.04

# Install required packages
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -qq -y install \
       wget \
       vim \
       sudo \
       git \
       build-essential \
       ninja-build \
       g++ \
       flex \
       python3 \
       ncurses-dev \
       libpcap-dev \
       language-pack-en \
       curl \
       zip \
       unzip \
       libssl-dev \
       gdb \
       autoconf automake \
       libpcre2-dev \
       bison \
       file \
       patchelf \
       squashfs-tools \
       libfuse2 \
       && rm -rf /var/lib/apt/lists/*

# Install more recent CMake
ARG CMAKE_VERSION=4.4.3

RUN mkdir ~/cmake-install \
       && cd ~/cmake-install \
       && wget https://github.com/Kitware/CMake/releases/download/v$CMAKE_VERSION/cmake-$CMAKE_VERSION.tar.gz \
       && tar -xzvf cmake-"$CMAKE_VERSION".tar.gz \
       && cd cmake-"$CMAKE_VERSION"/ \
       && ./bootstrap -- -DCMAKE_USE_OPENSSL=ON \
       && make -j$(nproc) \
       && make install

# Install SWIG
ARG SWIG_REPOSITORY=https://github.com/christophe-calmejane/swig.git
ARG SWIG_BRANCH=aggregated_release

RUN git clone -b $SWIG_BRANCH $SWIG_REPOSITORY --single-branch --depth 1 \
       && cd swig \
       && ./autogen.sh \
       && ./configure --prefix=/usr/local --with-pcre \
       && make -j$(nproc) \
       && make install

# Install dotnet 8.0 SDK
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get -qq -y install \
       dotnet-sdk-10.0 \
       && rm -rf /var/lib/apt/lists/*

# Add an environment variable to help differentiate between the host and the container
ENV IN_DOCKER_CONTAINER=1

# Create a user (since ubuntu 24.04, a non-root user 'ubuntu' is created by default with uid 1000)
# Unfortunately all previous helpers are using 'builder' user and paths are set accordingly, so we keep using 'builder' user here
# We need to delete the default 'ubuntu' user first to free up uid 1000 for compatibility with previous versions (access rights on mounted volumes)
ARG USERNAME=builder
ARG PASSWORD=builder

RUN userdel -r ubuntu 2>/dev/null || true
RUN useradd -rm -d /home/"$USERNAME" -s /bin/bash -g root -G sudo -u 1000 "$USERNAME"
RUN echo "$PASSWORD\n$PASSWORD" | passwd "$USERNAME"
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Switch to user
WORKDIR /home/$USERNAME
USER $USERNAME

# Install fnm and Node.js LTS
ENV FNM_DIR=/home/${USERNAME}/.fnm
ENV PATH=${FNM_DIR}:${PATH}

RUN curl -fsSL https://fnm.vercel.app/install | \
       bash -s -- --install-dir "${FNM_DIR}" --skip-shell \
       && fnm install --lts \
       && fnm default lts-latest \
       && echo 'eval "$(fnm env)"' >> /home/${USERNAME}/.bashrc

# Set UTF8 locale
RUN echo "export LANG=en_US.utf8" >> ~/.bashrc

# Create a folder to mount sources
RUN mkdir -p ~/sources

# Create a folder to mount build tree
RUN mkdir -p ~/builds

# Start in sources
WORKDIR /home/$USERNAME/sources

# Default command
CMD [ "bash" ]
