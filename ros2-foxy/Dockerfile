# syntax=docker/dockerfile:1.3
FROM nvidia/opengl:1.2-glvnd-runtime-ubuntu20.04
LABEL maintainer filip.zoric@fer.hr 

# Setup environment
ENV LANG C.UTF-8
ENV LC_AL C.UTF-8
ENV ROS_DISTRO foxy
ENV TZ=Europe/Zagreb 
ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES graphics,utility,compute 
ENV ROSCONSOLE_FORMAT '[${severity}] [${time}] [${node}]: ${message}'
ARG DEBIAN_FRONTEND=noninteractive

# Mitigate interactive prompt for choosing keyboard type
COPY ./keyboard /etc/default/keyboard

# Setup timezone 
RUN ln -snf /usr/share/zoneinfo/${TZ} /etc/localtime && echo ${TZ} > /etc/timezone 

RUN apt-get update 

# Install neccessary packages for ROS and Gazebo 
RUN apt-get update && apt-get install -q -y \
    apt-utils \
    build-essential \
    bc \
    cmake \
    curl \
    git \
    imagemagick \
    lsb-release \
    sudo \
    nano \
    net-tools \
    tmux \
    wget \
    python3-pip \
    gnupg 

RUN wget https://packages.osrfoundation.org/gazebo.gpg -O /usr/share/keyrings/pkgs-osrf-archive-keyring.gpg
RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/pkgs-osrf-archive-keyring.gpg] http://packages.osrfoundation.org/gazebo/ubuntu-stable $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/gazebo-stable.list > /dev/null
RUN apt-get update 

RUN apt-get install -y ignition-fortress

# Add keys for ROS2
RUN sh -c 'echo "deb [arch=amd64,arm64] http://repo.ros2.org/ubuntu/main `lsb_release -cs` main" > /etc/apt/sources.list.d/ros2-latest.list'
RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -

RUN apt-get update && apt-get install -y -q \
    locales \
    gnupg2 

# Setup language for ROS2
RUN locale-gen en_US en_US.UTF-8
RUN update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
RUN export LANG=en_US.UTF-8

# Install neccessary libs for ROS2
RUN apt-get install -y -q \
    libbullet-dev \
    python3-colcon-common-extensions \
    python3-flake8 \
    python3-pip \
    python3-pytest-cov \
    python3-rosdep \
    python3-setuptools \
    python3-vcstool 

# install some pip packages needed for testing
# https://answers.ros.org/question/345846/installing-ros2-dashing-from-source-fails-ubuntu-1910-eoan/
RUN python3 -m pip install -U \
    argcomplete \
    flake8-blind-except \
    flake8-builtins \
    flake8-class-newline \
    flake8-comprehensions \
    flake8-deprecated \
    flake8-docstrings \
    flake8-import-order \
    flake8-quotes \
    pytest-repeat \
    pytest-rerunfailures \
    pytest

# Install Fast-RTSP dependencies
RUN apt install --no-install-recommends -y \
    libasio-dev \
    libtinyxml2-dev \
    libcunit1-dev

# Add user and add it to sudo group
RUN adduser --disabled-password --gecos '' developer 
RUN adduser developer sudo 
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER developer 
WORKDIR /home/developer

# Install ROS2
RUN mkdir -p ros2_foxy/src
WORKDIR /home/developer/ros2_foxy
RUN wget https://raw.githubusercontent.com/ros2/ros2/foxy/ros2.repos
RUN vcs import src < ros2.repos
RUN sudo rosdep init && \
    rosdep update && \
    rosdep install --from-paths src --ignore-src --os=ubuntu:focal --rosdistro ${ROS_DISTRO} -y --skip-keys "fastcdr rti-connext-dds-5.3.1 urdfdom_headers"

