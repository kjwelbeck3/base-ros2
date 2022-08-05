FROM waggle/plugin-base:1.1.1-ml-cuda10.2-l4t
LABEL description="Waggle base image containing ROS2 foxy and major ML dependencies/frameworks"
# LABEL maintainer="Sage Waggle Team <sage-waggle@sagecontinuum.org>"
# LABEL url="https://github.com/waggle-sensor/plugin-base-images/tree/master/base-ros2"

ENV LANG=C.UTF-8
ENV LC_ALL=C.UTF-8
ENV ROS_DISTRO=foxy
ENV ROS_ROOT=/opt/ros/${ROS_DISTRO}
ENV ROS_PYTHON_VERSION=3
ENV ROS_WS=/root/overlay_ws

# install system packages
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    gnupg2 \
    lsb-release \
    net-tools \
    iputils-ping \
    nano \
    && rm -rf /var/lib/apt/lists/*

RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
    -o /usr/share/keyrings/ros-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) " \
       "signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] " \
       "http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" \
    | tee /etc/apt/sources.list.d/ros2.list > /dev/null \
    && apt update && apt install -y \
  build-essential \
  cmake \
  git \
  libbullet-dev \
  python3-colcon-common-extensions \
  python3-flake8 \
  python3-pip \
  python3-pytest-cov \dd
  python3-rosdep \
  python3-setuptools \
  python3-vcstool \
  wget

# install some pip packages needed for testing
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
    
# install Fast-RTPS dependencies
RUN apt install --no-install-recommends -y \
  libasio-dev \
  libtinyxml2-dev

# install Cyclone DDS dependencies
RUN apt install --no-install-recommends -y \
  libcunit1-dev


# Create workspace and clone all repos
RUN mkdir -p $ROS_ROOT/src
WORKDIR $ROS_ROOT
#RUN wget https://raw.githubusercontent.com/ros2/ros2/foxy/ros2.repos \
COPY rosinstall-base rosinstall-base
    && vcs import src < rosinstall-base

# Install ros dependencies with rosdep
RUN rosdep init && \
    rosdep update && \
    rosdep install --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-5.3.1 urdfdom_headers" 


# Build and source workspace
RUN colcon build --symlink-install \
    && echo "source $ROOT_WS/install/local_setup.bash" >> ~/.bashrc

# Creating and sourcing an empty overlay where other ros2 packages will be added
WORKDIR $ROS_WS
RUN mkdir -p src/  \
    && colcon build --symlink-install \
    && sh -c 'echo "source $ROS_WS/install/setup.bash" >> ~/.bashrc


