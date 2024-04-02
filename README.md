# ros-humble-ros1-bridge-builder
Create a "*ros-humble-ros1-bridge*" package for augre_msgs that can be used within Ubuntu 22.02 ROS2 Humble.

## How to create this builder docker image:

First, clone the repository:

Then, clone any custom messages into ros1_ws and ros2_ws folders within the repository (with their respective ROS versions).

My folders look like:
ros1-bridge-augre-msgs
  -ros1_ws
    --src
      ---augre_msgs
  -ros2_ws
    --augre_msgs

(NOTE: the file structure is different to download some ROS1 augre_msgs dependencies)

``` bash
  cd ros1-bridge-augre-msgs

  # By default, ros-tutorals support will be built: (bridging the ros-humble-example-interfaces package)
  docker build . -t ros1-bridge-augre-msgs
```

## How to create ros-humble-ros1-bridge package:
###  0.) Start from the latest Ubuntu 22.04 ROS 2 Humble Desktop system, create the "ros-humble-ros1-bridge/" ROS2 package:

``` bash
    cd ~/
    apt update; apt upgrade
    apt -y install ros-humble-desktop
    docker run --rm ros1-bridge-augre-msgs | tar xvzf -
```

## How to use ros-humble-ros1-bridge:

###  1.) Create an augre docker: https://github.com/UTNuclearRobotics/afc_demos
###  2.) Start the augre docker

``` bash
  rocker --x11 --user --home --privileged \
         --volume /dev/shm /dev/shm --network=host -- augre_docker \
         'bash -c "sudo apt update; sudo apt install -y tilix; tilix"'
```
You may need to install rocker first:
``` bash
  sudo apt install python3-rocker
```
Note: It's important to share the host's network and the `/dev/shm/` directory with the container.

###  3.) Then, start "roscore" inside the ROS1 Noetic docker container

``` bash
  source /opt/ros/noetic/setup.bash
  roscore
```

###  4.) Now, from the Ubuntu 22.04 ROS2 Humble system, start the ros1 bridge node.

You will also need to source a workspace with built ROS2 augre_msgs or it won't work.

``` bash
  source /opt/ros/humble/setup.bash
  source ~/ros-humble-ros1-bridge/install/local_setup.bash
  source ~/ros1-bridge-augre-msgs/ros2_ws/install/setup.bash
  ros2 run ros1_bridge dynamic_bridge
```
*Note: We need to source `local_setup.bash` and NOT `setup.bash` because the bridge was compiled in a docker container that may have different underlay locations.  Besides, we don't need to source these underlays in the host system again.

###  3.) Back to the ROS1 Noetic docker container, run in another terminal tab:

Right now, I've been having to build the afc_ws within the docker container--this should be fixed eventually.

``` bash
  source /opt/ros/noetic/setup.bash
  source ~/afc_ws/devel/setup.bash
  roslaunch afc_demos full_demo.launch
```

###  4.) Finally, from the Ubuntu 22.04 ROS2 Humble system:

``` bash
  source /opt/ros/humble/setup.bash
```
And run your corresponding nodes.

## References
- https://github.com/ros2/ros1_bridge
- https://github.com/ros2/ros1_bridge/blob/master/doc/index.rst
- https://github.com/smith-doug/ros1_bridge/tree/action_bridge_humble
- https://github.com/mjforan/ros-humble-ros1-bridge
- https://packages.ubuntu.com/jammy/ros-desktop-dev
- https://github.com/TommyChangUMD/ros-humble-ros1-bridge-builder
