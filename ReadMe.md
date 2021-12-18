## download gzmaze (environment as root directory)

```
cd ~/environment
git clone -b noetic  https://github.com/lbaitemple/gzmaze
```
##  setup gazebo-library
```
cd ~/environment
cd gzmaze
mkdir build
cd build
cmake ..
cd ..
source setup.bash
```
## setup ros
```
cd ~/environment
mkdir -p catkin_ws/src
cd gzmaze
cp -r jetbot_description ../catkin_ws/src/
cp -r pkg_tf_micromouse ../catkin_ws/src/
cd ../catkin_ws
rosdep install --from-paths src --ignore-src -r -y
colcon build
source install/setup.sh
roslaunch pkg_techfest_imc gazebo.launch
```
# What is gzmaze?
gzmaze is an attempt at flexing the power of [Gazebo](http://gazebosim.org)
The main goal here is to generate a maze in gazebo from a text file.

# How it works
There are two components, a GUI overlay plugin and a world plugin.

### GUI overlay
Gazebo has [a tutorial](http://gazebosim.org/tutorials?tut=gui_overlay&cat=user_input) on simple GUI overlays. I followed that tutorial, and extended it slightly.
There are two buttons and a textedit. When the buttons are clicked, a message is published to the topic ~/maze/regenerate

### World plugin
This is where the meat of the code is. We subscribe to ~/maze/regenerate and build mazes using gazebo messages. This plugin took example from the gazebo [Model editor](https://bitbucket.org/osrf/gazebo/src/default/gazebo/gui/model/). Essentially, it uses gazebo messages to construct Collision and Links. There links are then converted to and sdf::ElementPtr via the convenient funtions VisualToSDF and CollisionToSDF.

# Requirements
This code user to work with gazebo9, and but I've recently tested it with gazebo 11


cmake 2.8
Gazebo
```
sudo apt install gazebo9 libgazebo9-dev
```
To get the dependencies, you can probably just install from apt

# Building

    mkdir build
    cd build
    cmake .. && make

## on MacOS
cmake .. -DQt5_DIR=$(brew --prefix qt5)/lib/cmake/Qt5
# Running
Be sure to add the following to your `.gazebo/gui.ini` file

```
    [overlay_plugins]
    filenames=libregenerate_widget.so
```


    source setup.sh #this will setup the environment variables you need and run gazebo
    gazebo --verbose gzmaze.world

# Include model path 
$ export GAZEBO_MODEL_PATH=/usr/share/gazebo-11/models:~/mouse_ws/src/pkg_tf_micromouse/models:
