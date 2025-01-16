#FROM ubuntu:20.04
FROM nvidia/opengl:1.2-glvnd-devel-ubuntu20.04
ENV DEBIAN_FRONTEND noninteractive
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute

ARG CODE_DIR=/usr/local/src

RUN apt update

#basic environment
RUN apt install -y \
    ca-certificates \
    build-essential \
    git \
    cmake \
    cmake-curses-gui \
    libace-dev \
    libassimp-dev \
    libglew-dev \
    libglfw3-dev \
    libglm-dev \
    libeigen3-dev

# Suggested dependencies for YARP
RUN apt update && apt install -y \
    qtbase5-dev qtdeclarative5-dev qtmultimedia5-dev \
    qml-module-qtquick2 qml-module-qtquick-window2 \
    qml-module-qtmultimedia qml-module-qtquick-dialogs \
    qml-module-qtquick-controls qml-module-qt-labs-folderlistmodel \
    qml-module-qt-labs-settings \
    libqcustomplot-dev \
    libgraphviz-dev \
    libjpeg-dev \
    libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev \
    gstreamer1.0-plugins-base \
    gstreamer1.0-plugins-good \
    gstreamer1.0-plugins-bad \
    gstreamer1.0-libav

RUN apt install -y libhdf5-dev \
    lsb-release \
    curl

# Add metavision-sdk in sources.list
RUN echo "deb [arch=amd64 trusted=yes] https://apt.prophesee.ai/dists/public/b4b3528d/ubuntu focal sdk" >> /etc/apt/sources.list &&\
    apt update

RUN apt install -y \
    libcanberra-gtk-module \
    mesa-utils \
    ffmpeg \
    libboost-program-options-dev \
    libopencv-dev \
    metavision-sdk

#my favourites
RUN apt install -y \
    vim \
    gdb \
    libpython3-dev \
    python3.9-dev

# switch python3.9 as default
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.8 1
RUN update-alternatives --install /usr/bin/python3 python3 /usr/bin/python3.9 2
RUN apt install -y python3-pip \
    python3-tk

# install python package
Run pip3 install -U pip>=20.3

Run pip3 install poetry

# ROS NOETIC

RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -

RUN apt update && apt install -y ros-noetic-desktop-full


# Build Open Image Debugger
ARG OID_VERSION=v1.17.30
RUN cd $CODE_DIR && \
    git clone --depth 1 --branch $OID_VERSION https://github.com/OpenImageDebugger/OpenImageDebugger.git && \
    cd OpenImageDebugger && \
    git submodule init && \
    git submodule update && \
    cmake -S . -B build -DCMAKE_INSTALL_PREFIX=/usr/local && \
    cmake --build build --config Release --target install -j 4

# Build Google test
ARG GTEST_VERSION=v1.15.0
RUN cd $CODE_DIR && \
    git clone --depth 1 --branch $GTEST_VERSION https://github.com/google/googletest.git && \
    cd googletest && \
    mkdir build && cd build && \
    cmake .. && \
    make -j `nproc` install


    
# Add User ID and Group ID
ARG UNAME=root
ARG UID=1000
ARG GID=1000

# Change owner of some folders for the development
RUN chown -R $UNAME:$UNAME $CODE_DIR

USER $UNAME
WORKDIR /home/${UNAME}

# Add Open Image Debugger to non-root user
RUN touch ~/.gdbinit && \
    echo "source /usr/local/OpenImageDebugger/oid.py" > ~/.gdbinit