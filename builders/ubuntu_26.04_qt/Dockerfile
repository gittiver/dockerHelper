FROM builder:ubuntu_26.04

# Update apt sources (jammy has been moved to old-releases)
# RUN sed -i -r 's/([a-z]{2}.)?archive.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list && sed -i -r 's/security.ubuntu.com/old-releases.ubuntu.com/g' /etc/apt/sources.list

# Install additional packages
RUN sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get -qq -y install \
       '^libxcb.*-dev' libx11-xcb-dev libglu1-mesa-dev libxrender-dev libxi-dev libxkbcommon-dev libxkbcommon-x11-dev libfontconfig-dev fontconfig \
       && sudo rm -rf /var/lib/apt/lists/*

# Install more recent Qt
ARG QT_VERSION=6.8.3

RUN git clone https://code.qt.io/qt/qt5.git qt6 \
       && cd qt6 \
       && git checkout "$QT_VERSION" \
       && perl init-repository --module-subset=essential \
       && mkdir _build \
       && cd _build \
       && ../configure -opensource -nomake examples -nomake tests -confirm-license -skip webengine -skip 3d -skip location -no-opengl -fontconfig \
       && cmake --build . --parallel \
       && sudo cmake --build . --target install \
       && cd ../.. \
       && rm -rf qt6
# Qt is installed in /usr/local/Qt-$QT_VERSION
# Qt6Config.cmake is installed in /usr/local/Qt-$QT_VERSION/lib/cmake

# Create a ld.so.conf.d file to help find Qt
RUN sudo sh -c "echo "/usr/local/Qt-${QT_VERSION}/lib" > /etc/ld.so.conf.d/qt.conf" \
       && sudo ldconfig

# Add Qt bin to PATH
RUN echo "export PATH=/usr/local/Qt-$QT_VERSION/bin:\$PATH" >> ~/.bashrc
