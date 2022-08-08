FROM ubuntu:focal AS focal-base

ARG L4T_RELEASE_MAJOR=32.4
ARG L4T_RELEASE_MINOR=4
ARG CUDA=10.2
ARG DEBIAN_FRONTEND=noninteractive
ARG SOC="t210"

ARG L4T_RELEASE=$L4T_RELEASE_MAJOR.$L4T_RELEASE_MINOR

RUN apt-get update && \
    apt-get install -y --no-install-recommends apt-utils software-properties-common && \
    apt-get upgrade -y && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

#
# Jetson debian packages
#
RUN echo $L4T_RELEASE_MAJOR
ADD --chown=root:root https://repo.download.nvidia.com/jetson/jetson-ota-public.asc /etc/apt/trusted.gpg.d/jetson-ota-public.asc
RUN chmod 644 /etc/apt/trusted.gpg.d/jetson-ota-public.asc && \
    apt-get update && apt-get install -y --no-install-recommends ca-certificates && \
    echo "deb https://repo.download.nvidia.com/jetson/common r$L4T_RELEASE_MAJOR main" > /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    echo "deb https://repo.download.nvidia.com/jetson/${SOC} r$L4T_RELEASE_MAJOR main" >> /etc/apt/sources.list.d/nvidia-l4t-apt-source.list && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

#
# Tegra setup
#
RUN apt-get update && \
    apt-get install -y libglu1-mesa-dev freeglut3 freeglut3-dev unzip dialog && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

RUN echo "/usr/lib/aarch64-linux-gnu/tegra" >> /etc/ld.so.conf.d/nvidia-tegra.conf && \
    echo "/usr/lib/aarch64-linux-gnu/tegra-egl" >> /etc/ld.so.conf.d/nvidia-tegra.conf
RUN rm /usr/share/glvnd/egl_vendor.d/50_mesa.json
RUN mkdir -p /usr/share/glvnd/egl_vendor.d/ && \
    echo '{"file_format_version" : "1.0.0" , "ICD" : { "library_path" : "libEGL_nvidia.so.0" }}' > /usr/share/glvnd/egl_vendor.d/10_nvidia.json
RUN mkdir -p /usr/share/egl/egl_external_platform.d/ && \
    echo '{"file_format_version" : "1.0.0" , "ICD" : { "library_path" : "libnvidia-egl-wayland.so.1" }}' > /usr/share/egl/egl_external_platform.d/nvidia_wayland.json
RUN echo "/usr/local/cuda-$CUDA/targets/aarch64-linux/lib" >> /etc/ld.so.conf.d/nvidia.conf

RUN ldconfig

#
# Update environment
#
ENV PATH /usr/local/cuda-$CUDA/bin:/usr/local/cuda/bin:${PATH}
ENV LD_LIBRARY_PATH /usr/local/cuda-$CUDA/targets/aarch64-linux/lib:${LD_LIBRARY_PATH}
ENV LD_LIBRARY_PATH=/opt/nvidia/vpi1/lib64:${LD_LIBRARY_PATH}
ENV LD_LIBRARY_PATH=/usr/lib/aarch64-linux-gnu/tegra:${LD_LIBRARY_PATH}
ENV LD_LIBRARY_PATH=/usr/lib/aarch64-linux-gnu/tegra-egl:${LD_LIBRARY_PATH}

ENV NVIDIA_VISIBLE_DEVICES all
ENV NVIDIA_DRIVER_CAPABILITIES all
ENV OPENBLAS_CORETYPE=ARMV8

#WORKDIR /root
#CMD ["bash"]


FROM focal-base AS base-with-opencv

ARG DEBIAN_FRONTEND=noninteractive
ARG OPEN_CV_VERSION=4.5.0

# 
# overwrite with cuda opencv
#

RUN apt-get update && apt-get install -y --no-install-recommends python3-pip python3-dev unzip && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get clean

WORKDIR /tmp

ADD assets/OpenCV-${OPEN_CV_VERSION}-aarch64-ubuntu20.zip .

# RUN unzip OpenCV-${OPEN_CV_VERSION}-aarch64-ubuntu20.zip && \
#     apt update && \
#     apt install -y --no-install-recommends -f ./*.deb && \
#     rm -rf /var/lib/apt/lists/* && \
#     rm -rf * \
#     apt-get clean

# #install missing numpy
# RUN pip3 install numpy


# FROM base-with-opencv AS base-with-opencv-pytorch

# ARG DEBIAN_FRONTEND=noninteractive
# ARG BUILD_SOX=1
# ARG PYTORCH_VERSION=1.11.0

# # 
# # install pytorch deps
# #
# RUN apt-get update && apt-get install -y --no-install-recommends python3-pip python3-dev libopenblas-dev libjpeg-dev zlib1g-dev sox libsox-dev libsox-fmt-all unzip && \
#     rm -rf /var/lib/apt/lists/* && \
#     apt-get clean

# SHELL ["/bin/bash", "-c", "-l"]

# # 
# # install prebuild pytorch binaries
# #
# WORKDIR /tmp/
# COPY assets/torch-${PYTORCH_VERSION}-aarch64-ubuntu20.zip . 
# RUN unzip torch-${PYTORCH_VERSION}-aarch64-ubuntu20.zip && \
#     pip3 install *.whl && \
#     rm -rf *

# # 
# # install tensorrt python bindings
# #
# COPY assets/tensorrt-8.2.1.8-cp38-none-linux_aarch64.whl . 
# RUN pip3 install *.whl && \
#     rm -rf *

# ENV PATH=/usr/local/cuda/bin:/usr/local/cuda-10.2/bin:/usr/local/cuda/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
# ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/cuda-10.2/targets/aarch64-linux/lib:





# FROM base-with-opencv-pytorch

# ARG DEBIAN_FRONTEND=noninteractive

# ARG ROS_PKG=ros_base
# ARG ROS_DISTRO=foxy
# ARG ROS_ROOT=/opt/ros/${ROS_DISTRO}

# #
# # change the locale from POSIX to UTF-8
# #
# RUN apt-get update && \
#     apt-get install -y --no-install-recommends \
#     locales \
#     && rm -rf /var/lib/apt/lists/* \
#     && apt-get clean
# RUN locale-gen en_US en_US.UTF-8 && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
# ENV LANG=en_US.UTF-8
# ENV PYTHONIOENCODING=utf-8

# # 
# # add the ROS deb repo to the apt sources list
# #
# RUN apt-get update && \
#     apt-get install -y --no-install-recommends \
# 		curl \
# 		wget \
# 		gnupg2 \
# 		lsb-release \
# 		ca-certificates \
#     && rm -rf /var/lib/apt/lists/* \
#     && apt-get clean

# RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key  -o /usr/share/keyrings/ros-archive-keyring.gpg
# RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

# # 
# # install ros2 packages
# #
# RUN apt-get update && \
#     apt-get install -y --no-install-recommends \
# 		ros-${ROS_DISTRO}-ros-base \
# 		python3-colcon-common-extensions \
#     && rm -rf /var/lib/apt/lists/* \
#     && apt-get clean

# # source ROS
# RUN echo "source /opt/ros/${ROS_DISTRO}/setup.bash" >> /root/.bashrc

# ENV ROS_WS=/root/overlay_ws

# # creating and sourcing an empty overlay where other ros2 packages will be added
# RUN mkdir -p $ROS_WS/src/
# WORKDIR $ROS_WS
# RUN colcon build --symlink-install \
#     && sh -c 'echo "source $ROS_WS/install/setup.bash" >> ~/.bashrc'

