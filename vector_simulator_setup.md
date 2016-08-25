#Reference:
-https://github.com/StanleyInnovation/vector_v1/wiki/Setup-Instructions

#Install ROS
-sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'
-sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net --recv-key 0xB01FA116
-sudo apt-get update
-sudo apt-get install ros-indigo-desktop-full
-sudo rosdep init
-rosdep update
-echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
-source ~/.bashrc
-sudo apt-get install python-rosinstall

#Install additional packages
-sudo apt-get install ros-indigo-control-toolbox ros-indigo-moveit-core ros-indigo-costmap-2d ros-indigo-move-base ros-indigo-jsk-recognition ros-indigo-controller-manager ros-indigo-gazebo-ros* ros-indigo-hector* ros-indigo-rviz-imu-plugin ros-indigo-robot-pose-ekf ros-indigo-robot-localization ros-indigo-yocs-cmd-vel-mux ros-indigo-joint-*

#Create a Software folder (optional)
-mkdir ~/Software

#Freenect2
-cd ~/Software
-git clone https://github.com/OpenKinect/libfreenect2.git
-cd libfreenect2
-cd depends; ./download_debs_trusty.sh
-sudo dpkg -i debs/libusb*deb
-sudo apt-get install libturbojpeg libjpeg-turbo8-dev
-sudo dpkg -i debs/libglfw3*deb; sudo apt-get install -f; sudo apt-get install libgl1-mesa-dri-lts-vivid
-cd ..
-mkdir build && cd build
-cmake .. -DCMAKE_INSTALL_PREFIX=~/Software/freenect2 -Dfreenect2_DIR=~/Software/freenect2/lib/cmake/freenect2
-make
-make install
-sudo ln -sf ~/Software/freenect2/lib/cmake/freenect2/freenect2Config.cmake /usr/share/cmake-2.8/Modules/Findfreenect2.cmake

#Install NLOpt
-sudo apt-get install libnlopt-dev

#Vector Install
-mkdir -p ~/Software/stanley
-cd ~/Software/stanley
-git clone git@github.com:StanleyInnovation/vector_v1.git
-mkdir -p ~/vector_ws/src
-ln -s ~/Software/stanley/vector_v1/ ~/vector_ws/src/
-vi ~/vector_ws/src/vector_v1/vector_network/env-hooks/50.vector_network_config.sh
-For simulation, uncomment "export ROBOT_NETWORK=eth0" and comment out "export ROBOT_NETWORK=br0"
-For two arms, edit ~/vector_ws/src/vector_v1/vector_common/vector_config/vector_config.sh accordingly (set VECTOR_HAS_TWO_KINOVA_ARMS and VECTOR_HAS_TWO_ROBOTIQ_GRIPPERS to true)
-cd ~/vector_ws/src
-catkin_init_workspace

#Fix for simulation
-sudo gedit /opt/ros/indigo/share/hector_pose_estimation/hector_pose_estimation_nodelets.xml
-Paste the following into the file:
<library path="lib/libhector_pose_estimation_nodelet">
  <class name="hector_pose_estimation/PoseEstimationNodelet" type="hector_pose_estimation::PoseEstimationNodelet" base_class_type="nodelet::Nodelet">
  <description>
    This nodelet initializes the pose estimation filter with a generic system model driven by IMU measurements only.
  </description>
  </class>
</library>

#HLP-R Manipulation Stack
-sudo apt-get install ros-indigo-ecl-geometry
-sudo apt-get install ros-indigo-moveit-full
-cd ~/Software
-git clone https://bitbucket.org/traclabs/trac_ik.git
-ln -s ~/Software/trac_ik ~/vector_ws/src
-git clone -b vector-develop https://github.com/GT-RAIL/wpi_jaco.git
-ln -s ~/Software/wpi_jaco/wpi_jaco_msgs/ ~/vector_ws/src/
-ln -s ~/Software/wpi_jaco/wpi_jaco_wrapper/ ~/vector_ws/src/
-ln -s ~/Software/wpi_jaco/jaco_sdk/ ~/vector_ws/src/
-git clone https://github.com/GT-RAIL/rail_manipulation_msgs.git
-ln -s ~/Software/rail_manipulation_msgs/ ~/vector_ws/src/
-cd stanley
-git clone https://github.com/StanleyInnovation/robotiq_85_gripper.git
-ln -s ~/Software/stanley/robotiq_85_gripper/robotiq_85_msgs/ ~/vector_ws/src/
-cd ~/Software
-mkdir HLP-R && cd HLP-R
-git clone git@github.com:HLP-R/hlpr_manipulation.git
-ln -s ~/Software/HLP-R/hlpr_manipulation/ ~/vector_ws/src/


#HLP-R Simulation stack
-cd ~/Software/HLP-R
-git clone git@github.com:HLP-R/hlpr_simulator.git
-ln -s ~/Software/HLP-R/hlpr_simulator/ ~/vector_ws/src/


#Navigation
-sudo apt-get install ros-indigo-navigation
-sudo apt-get install ros-indigo-slam-gmapping
-For simulation, edit ~/vector_ws/src/vector_v1/vector_common/vector_config/vector_config.sh and change line 27 to "export VECTOR_USE_PLATFORM_ODOMETRY=false"


#Build and Test
-Open a new terminal
-cd ~/vector_ws
-catkin_make
-source devel/setup.bash
-roslaunch hlpr_gazebo vector.launch



