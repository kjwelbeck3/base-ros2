# Base ROS2 Images

## Working Containers

### Pywaggle Base + ROS2 Foxy

Directory: `base-ros2-with-empty-overlay`
DockerHub registry: `kwelbeck/base-ros2-with-empty-overlay:ml`
Architecture: amd64, arm64
Description:

- Waggle Base Image for ROS2-enabled containers
- Ubuntu 22.04 Focal
- ROS2 Foxy base requirements
  
### Pywaggle Base ML with CUDA 10.2 + ROS2 Dashing (EOL)

Directory: `base-ros2-ml_18.04_dashing_r32.4.4`
DockerHub registry: `kwelbeck/base-ros2-with-empty-overlay:ml`
Architecture: amd64, arm64
Description:

- Waggle Base Image for running ROS2- and CV/ML-enabled containers on Jetson Nano
- Ubuntu 18.04 Bionic
- ROS2 Dashing base requirements
- Waggle-provisioned
- Jetpack 4.4.1 ready for ML applications
  - L4T version: r32.4.4
  - Courtesy of `nvcr.io/nvidia/l4t-ml:r32.4.4-py3` nested image
  
**NOTE**: ROS2 Dashing is no longer officially supported (EOL = May 2021). Communication with officially supported ROS2 versions (Foxy and Humble) is still possible as per normal.

## Containers in development

### 1

Directory: `base-ros2-ml_20.04_foxy_r32.7.1`
Description:

- [TODO]
- ROS2 Foxy + Waggle + ML for L4T version r32.7.1
- Can this run on Jetpack 4.4.1 compatibly?

### 2

Directory: `.base-ros2-ml_18.04_foxy.from.source_r32.4.4`
Description:

- [TODO]
- ROS2 Foxy + Waggle + ML for L4T version r32.4.4
- Reference: <https://github.com/dusty-nv/jetson-containers>
