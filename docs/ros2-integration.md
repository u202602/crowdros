# ROS 2 Control

If `Enable Keyboard` is unchecked, the robot can be controlled from ROS 2 by publishing to the robot namespace `cmd_vel` topic.

## Install ROS 2 Jazzy

Install ROS 2 on Windows using the official Jazzy binary instructions:

https://docs.ros.org/en/jazzy/Installation/Windows-Install-Binary.html#windows-binary

Install ROS 2 on Ubuntu using the official Jazzy Debian package instructions:

https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

## Build `ros_tcp_endpoint`

1. Open a ROS 2 shell.
2. Copy `ros_tcp_endpoint` into your ROS 2 workspace `src` folder.
3. Build the workspace:

```powershell
colcon build
```

## Start the ROS TCP Endpoint

In a ROS 2 shell, run:

```powershell
ros2 run ros_tcp_endpoint default_server_endpoint --ros-ip 127.0.0.1 --ros-port 10000
```

## Publish Robot Velocity Commands

Open another ROS 2 shell and publish to the robot namespace `cmd_vel` topic. For example:

```powershell
ros2 topic pub /robot_11/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 1, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

## Visualize Sensors in RViz

With a ROS 2 shell active, run:

```powershell
ros2 run rviz2 rviz2 --ros-args -p use_sim_time:=true
```
