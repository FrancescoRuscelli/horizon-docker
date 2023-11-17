# Use a build argument for the ROS version
ARG ROS_VERSION=noetic

# Use the specified ROS version as the base image
FROM ros:${ROS_VERSION}-robot

# after every FROM statement, all the ARGs are no longer available, this solves it:
ARG ROS_VERSION

# Update package lists and install dependencies
RUN apt-get update && apt-get install -y sudo build-essential git curl python3-tk ros-noetic-rosbridge-suite python3-pip libjpeg-dev cmake-curses-gui

# required for casadi and casadi_kin_dyn
RUN apt-get install -y libassimp-dev liblapack-dev libblas-dev libyaml-cpp-dev libmatio-dev

# required for displaying matplotlib plots
ENV DISPLAY :1
# add user with sudo privileges which is not prompted for password
RUN adduser --disabled-password --gecos '' user
RUN adduser user sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER user
WORKDIR /home/user
ENV PATH="/home/user/.local/bin:${PATH}"

# # here it gets installed
# RUN cd ~/ws && rosdep update && rosdep install --from-paths src --ignore-src -r -y
# RUN . /opt/ros/melodic/setup.sh && cd ~/ws && catkin_make install

# copy spot-ros-pkg in install folder so that rvizweb finds it
RUN mkdir -p ws/ros_src

RUN cd ~/ws/ros_src && git clone https://github.com/FrancescoRuscelli/spot-ros-pkg

# source workspace for ROS_PACKAGE_PATH
RUN echo "export ROS_PACKAGE_PATH=/home/user/ws/ros_src" > .bashrc

# restart bash to make the source effective
SHELL ["bash", "-ic"]
RUN echo $ROS_PACKAGE_PATH

# break cache for re-installation of casadi (to always download the last version)
ARG CACHE_DATE="date"

# install casadi-horizon
# RUN pip3 install casadi-horizon

# python3 -m horizon.examples.spot --r --action walk
# overwrite ros launch to enable rvizweb
# COPY rviz/launcher.launch /home/user/.local/lib/python3.6/site-packages/horizon/examples/replay/launch/

# # add a configuration for the rvizweb (robot model, grid, world...)
# COPY configuration.json /home/user/ws/install/share/rvizweb/config/

#COPY examples.sh examples.sh
#CMD sudo chmod +x examples.sh && ./examples.sh