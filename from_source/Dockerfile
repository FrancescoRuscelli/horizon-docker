# Use a build argument for the ROS version
ARG ROS_VERSION=noetic

# Use the specified ROS version as the base image
FROM ros:${ROS_VERSION}-robot

# after every FROM statement, all the ARGs are no longer available, this solves it:
ARG ROS_VERSION

# Update package lists and install dependencies
# python3-tk libjpeg-dev
RUN apt-get update && apt-get install -y sudo build-essential gfortran git curl python3-pip python3-tk wget patchelf
# install rviz for visualization
RUN apt-get install -y ros-noetic-rviz
# required for casadi
RUN apt-get install -y libassimp-dev liblapack-dev libblas-dev libyaml-cpp-dev libmatio-dev
# required for casadi python bindings
RUN apt-get install -y swig

# Install ROS catkin tools
# RUN apt-get install -y ros-${ROS_VERSION}-catkin

# Upgrade pip and install the latest version of NumPy 
# only required for ROS noetic
RUN pip3 install --upgrade pip \
    && pip3 install --upgrade numpy

RUN pip3 install hhcm-forest

# required packages for horizon
RUN pip3 install PyQt5 scipy numpy_ros matplotlib 

# Download and install CMake version 3.15
# only required for melodic
# RUN wget --no-check-certificate https://cmake.org/files/v3.15/cmake-3.15.0-Linux-x86_64.sh && \
#     chmod +x cmake-3.15.0-Linux-x86_64.sh && \
#     ./cmake-3.15.0-Linux-x86_64.sh --skip-license --prefix=/usr/local && \
#     rm cmake-3.15.0-Linux-x86_64.sh

# required for displaying matplotlib plots
ENV DISPLAY :1
# add user with sudo privileges which is not prompted for password
RUN adduser --disabled-password --gecos '' user
RUN adduser user sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER user
WORKDIR /home/user
ENV PATH="/home/user/.local/bin:${PATH}"

# set up forest for installation
RUN mkdir forest_ws

WORKDIR /home/user/forest_ws
RUN forest init 

# source forest
RUN echo "source ~/forest_ws/setup.bash" >> ~/.bashrc

# # restart bash to make the source effective
SHELL ["bash", "-ic"]
RUN echo $ROS_PACKAGE_PATH

# add custom recipes
ADD recipes /home/user/forest_ws/recipes

# install pybind11 ---> because ros-noetic-catkin install pybind11_cmake, that is found from forest, that won't install its version
#                          but casadi_kin_dyn, trying to include <pybind11/pybind11>, 
#                          fails to find it because it should include it like <pybind11_cmake/pybind11/pybind11>

# RUN forest grow pybind11 --clone-protocol https -j7

# install horizon
RUN forest grow horizon --clone-protocol https -j7 --verbose

# python3 -m horizon.examples.spot --r --action walk

# # install catkin workspace
# WORKDIR /home/user/
# RUN mkdir -p kyon_ws/src
# RUN cd ~/kyon_ws && rosdep update && rosdep install --from-paths src --ignore-src -r -y
# RUN . /opt/ros/${ROS_VERSION}/setup.sh && cd ~/kyon_ws && catkin_make install

# source forest
# RUN echo "source ~/forest_ws/setup.bash" >> ~/.bashrc

# pip install -e . --no-deps --verbose
# # restart bash to make the source effective
# SHELL ["bash", "-ic"]

# clone and install phase_manager
# RUN cd ~/forest_ws/src && git clone https://github.com/FrancescoRuscelli/phase_manager
# RUN mkdir -p ~/forest_ws/build/phase_manager
# WORKDIR /home/user/forest_ws/build/phase_manager
# RUN cmake ../../src/phase_manager -DCMAKE_INSTALL_PREFIX=/home/user/forest_ws/install .
# RUN make -j8 install

# # # break cache for re-installation of kyon (to always download the last version)
# ARG CACHE_DATE="date"

# # Clean up package lists to reduce image size
# RUN apt-get clean && \
#     rm -rf /var/lib/apt/lists/*


# roslaunch kyon_codesign lower_body_action.launch action:=trot

