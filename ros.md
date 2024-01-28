# ros

## ros1

### bag
```bash
# show info
rosbag info <bag_file_name>

# install ros_readbagfile
# download script
wget https://raw.githubusercontent.com/ElectricRCAircraftGuy/eRCaGuy_dotfiles/master/useful_scripts/ros_readbagfile.py
# make script executable
chmod +x ros_readbagfile.py
# move to bin
mkdir -p ~/bin
mv ros_readbagfile.py ~/bin/ros_readbagfile
# add export PATH="$HOME/bin:$PATH" in .bashrc
source ~/.bashrc
ros_readbagfile -h

# extract message by topic
ros_readbagfile <bag_file> <topic1> >> <output_file>

# play bag
# in terminal 1
roscore
# in terminal 2
rosbag play -i <bag_file>
```

### rqt

```bash
rqt_bag
```

### rviz

```bash
roscore

```

### velodyne
```bash
sudo apt-get install libpcap0.8-dev
sudo apt-get install ros-noetic-velodyne-*
sudo apt-get install ros-noetic-velodyne

```

## ros2

### use foxy using docker
```bash
# pull image
docker pull ros:foxy

# run docker name=foxy
docker container run --name foxy -it ros:latest

# enter docker
docker exec -it foxy /bin/bash

# source setup
source ./ros_entrypoint.sh

# backup container
docker commit -p <container_id> <image_name>

# run backup
docker container run --name foxy -it <image_name>:latest
```

### install galactic on ubuntu focal

<https://docs.ros.org/en/galactic/Installation/Ubuntu-Install-Debians.html>

### basic

```bash
#
rqt

#
rqt_graph

# run
ros2 run <package_name> <executable_name>

# run with param
ros2 run <package_name> <executable_name> --ros-args --params-file <file_name>

# run with default log level
ros2 run turtlesim turtlesim_node --ros-args --log-level WARN

#  rqt_console
ros2 run rqt_console rqt_console

# run from launch file
ros2 launch <launch_file>

# doctor
```

### node

```bash
# list
ros2 node list

# node
ros2 node info <node_name>
```

### topic

```bash
# list
ros2 topic list

# show message
ros2 topic echo <topic_name>

# show msg type & publisher & subscriber
ros2 topic info <topic_name>

# show msg structure
ros2 interface show <msg_type>

# pub msg
ros2 topic pub <topic_name> <msg_type> '<data_yaml>' --once
ros2 topic pub <topic_name> <msg_type> '<data_yaml>' --rate 1

# show msg rate
ros2 topic hz <topic_name>
```

### service

```bash
# list
ros2 service list
ros2 service list -t

# show service type
ros2 service type <service_name>

# find service by type
ros2 service find <type_name>

# show service structure
ros2 interface show <service_type>

# call
ros2 service call <service_name> <service_type> <arguments>
```

### param

```bash
# list
ros2 param list

# get
ros2 param get <node_name> <parameter_name>

# set
ros2 param set <node_name> <parameter_name> <value>

# dump all node param
ros2 param dump <node_name>

# load param
ros2 param load <node_name> <param_file>
```

### action

```bash
# list
ros2 action list -t

# info
ros2 action info <action_name>

# show action type
ros2 interface show <action_type>

# send goal
ros2 action send_goal <action_name> <action_type> <values>
ros2 action send_goal <action_name> <action_type> <values> --feedback
```

### bag

```bash
# record
ros2 bag record <topic_name>

# show info
ros2 bag info <bag_file_name>
```

### package

```bash
# create
ros2 pkg create --build-type ament_cmake --node-name <node_name> <package_name>
ros2 pkg create --build-type ament_cmake <package_name> --dependencies <dep1> <dep2>


# check dep
rosdep install -i --from-path src --rosdistro galactic -y

# build all packages
colcon build

# build one package
colcon build --packages-select <package_name>

# will create build  install  log dirs

# open new terminal

# source underlay
source /opt/ros/galactic/setup.bash

# source overlay
. install/local_setup.bash
```
