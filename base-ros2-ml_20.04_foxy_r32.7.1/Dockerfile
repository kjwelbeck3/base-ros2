FROM timongentzsch/l4t-ubuntu20-ros2-base:latest

ENV ROS_WS=/root/overlay_ws

# creating and sourcing an empty overlay where other ros2 packages will be added
RUN mkdir -p $ROS_WS/src/
WORKDIR $ROS_WS
RUN colcon build --symlink-install \
    && sh -c 'echo "source $ROS_WS/install/setup.bash" >> ~/.bashrc'
