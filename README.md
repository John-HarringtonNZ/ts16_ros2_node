# ts16_ros2_node

This is a ROS2 driver for the TS16 Leica-Geosystems Robotic Total Station. Base code and README come from [ROS version repository](https://github.com/arpit6232/Leica_Total_Station_ROS).

## Installation

This instructions are for Ubuntu 20.04 with ROS Galactic/Foxy already installed.

Clone this repo into your catkin_ws (the code below creates a new ROS2 workspace named `ts_ws` in your home folder):


```
mkdir -p  ~/ts_ws/src && cd ~/ts_ws/src
git@github.com:John-HarringtonNZ/ts16_ros2_node.git
```

and install dependencies

```
sudo apt install python-dev python-pip
sudo -H python -m pip install --upgrade pip
sudo -H python -m pip install pyserial
```

Then build the workspace

```
cd ~/ts_ws
colcon build
```

## Get Started

Connect the TS16 to your compute platform using the provided RS232 cable connection. Note what serial port name that appears and update the config file appropraitely. Ensure that the TS16 is on before launching the driver node. Once it is on, launch the node using the following command:

`ros2 launch total_station total_station_launch.py`

Once this is launched, it is expected that there will be termimal warnings printing out, since the current data streaming is not in the valid format (since the total station is not currentl measuring). 

In order to get real-time positioning from the TS16 platform, it's important to use the "Autopoint" feature of the TS16. By default, the author has been able to get ~6Hz output from this alone, but it means that the TS16 is locally saving every point it streams. To avoid this the author has implemented a "hack" that tricks the system into maintaining autopoint mode but not to save the data. This is acheived through the following order of settings:

1. Enable tracking by using the Power Search Feature
 - Confirm track by listening for the “Target Locked” announcement
2. Go to Home -> Measure, Press ‘pg’ button to get to “Autopoint” tab
3. Start “Autopoint” mode
    -  Stop auto point mode after ~100 points
4. Move over to the first measure tag using the ‘pg’ button.
7. Turn on measure mode, using “Measure button”, then it should start to broadcast at a higher frequency 
 - Measurement should start automatically and begin streaming data (while not recording data on TS)
8. Check using command: `ros2 topic hz /total_station_prism`
 - The expected Hz should be around 7-9.
