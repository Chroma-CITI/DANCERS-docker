FROM osrf/ros:humble-desktop-full-jammy

ENV DEBIAN_FRONTEND=noninteractive

# install necessary packages
RUN apt update && apt install -y --no-install-recommends \
    git \
    cmake \
    wget \
    lsb-release \
    gnupg \
    libqt5gui5 \
    ubuntu-gnome-desktop \
    g++ \
    python3 \
    freeglut3-dev \
    tmux \
    nano \
    gdb

# ========== ns-3 ==========
# Download ns-3 and unpack it
WORKDIR /usr
RUN wget https://www.nsnam.org/release/ns-allinone-3.42.tar.bz2 && \
tar -jxvf ns-allinone-3.42.tar.bz2

# install ns-3 in /usr/ns3
RUN cd ns-allinone-3.42/ns-3.42 &&\
./ns3 configure --prefix=/usr/ns3 &&\
./ns3 install

# clean ns-3 archive
RUN rm ns-allinone-3.42.tar.bz2

# ========== user ==========
ARG USERNAME=choreographer

# Create and use a user for the simulation
RUN useradd --system --user-group --create-home --shell /bin/bash --groups sudo $USERNAME
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER $USERNAME
WORKDIR /home/$USERNAME

RUN echo "set -g prefix C-a \nunbind C-b \nbind C-a send-prefix" >> ~/.tmux.conf

# Add ns3 to LD_LIBRARY_PATH so that shared libraries can be found
RUN echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/ns3/lib" >> ~/.bashrc && \
    echo "source /opt/ros/humble/setup.bash" >> ~/.bashrc && \
    echo "source /home/$USERNAME/sim_ws/install/setup.bash" >> ~/.bashrc

# ========== DANCERS ==========
ARG CACHE_BUST=1
WORKDIR /home/$USERNAME
# Get the source of the DANCERS components
RUN git clone https://github.com/Chroma-CITI/DANCERS sim_ws --recursive

ENV ROS_WS=/home/$USERNAME/sim_ws

WORKDIR /home/$USERNAME/sim_ws

# Build all the ROS nodes (-i to run bash iteratively so that ~/.bashrc does not exit early | CMAKE -w flag to disable CMAKE warnings)
RUN /bin/bash -c -i "colcon build --cmake-args -DCMAKE_CXX_FLAGS='-w' --packages-ignore gazebo_sim multirobot_control viragh_connector px4_msgs obstacle_detector offboard_flocking"

WORKDIR /home/$USERNAME
