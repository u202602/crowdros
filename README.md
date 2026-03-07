# CrowdROS: A Crowd Simulation Platform for Social Robot Navigation

CrowdROS is a simulation platform for building crowd navigation scenarios, running social robot experiments, visualizing recorded trajectories, and computing robot and crowd metrics from saved runs.

## Download and Extract

Download the latest binaries from:

https://github.com/u202602/crowdros/releases

The release packages include both Windows and Linux versions. Unzip the downloaded files and continue the rest of this README from inside the `Build` folder.

## Running the Simulator

Launch the simulator from the project root:

```powershell
.\CrowdROS.exe
```

On Linux, run:

```bash
./build.x86_64
```

## Basic Workflow

1. Select a scene from the scene dropdown.
2. Create a scenario with the `Add Scenario` button.
3. Choose how to control the robot with the `Enable Keyboard` checkbox:
   - Enabled: control the robot manually with the keyboard.
   - Disabled: control the robot by publishing ROS 2 `cmd_vel` messages.
4. Press `Start` to run the selected scene and scenario.

## Creating a Scenario

When creating a scenario in the GUI:

1. Place human agents and robot agents with left click.
2. Place goals with right click.
3. After placing all agents and goals, click `Next`.
4. Configure each agent:
   - For humans: choose the agent type, crowd policy, and preferred speed (between 1 and 5 m/s).
   - For robots: choose the robot type.
5. Enter a scenario name and click `Save`.

## Controls

### Camera Controls

- `[` : move camera up
- `]` : move camera down
- `Arrow keys` : move camera
- `Q` / `E` : rotate camera

### Robot Controls

- `W`, `A`, `S`, `D` : move robot

## ROS 2 Control

If `Enable Keyboard` is unchecked, the robot can be controlled from ROS 2 by publishing to the robot namespace `cmd_vel` topic.

### Install ROS 2 Jazzy

Install ROS 2 on Windows using the official Jazzy binary instructions:

https://docs.ros.org/en/jazzy/Installation/Windows-Install-Binary.html#windows-binary

Install ROS 2 on Ubuntu using the official Jazzy Debian package instructions:

https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html

### Build `ros_tcp_endpoint`

1. Open a ROS 2 shell.
2. Copy `ros_tcp_endpoint` into your ROS 2 workspace `src` folder.
3. Build the workspace:

```powershell
colcon build
```

### Start the ROS TCP Endpoint

In a ROS 2 shell, run:

```powershell
ros2 run ros_tcp_endpoint default_server_endpoint --ros-ip 127.0.0.1 --ros-port 10000
```

### Publish Robot Velocity Commands

Open another ROS 2 shell and publish to the robot namespace `cmd_vel` topic. For example:

```powershell
ros2 topic pub /robot_11/cmd_vel geometry_msgs/msg/Twist "{linear: {x: 1, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}"
```

### Visualize Sensors in RViz

With a ROS 2 shell active, run:

```powershell
ros2 run rviz2 rviz2 --ros-args -p use_sim_time:=true
```

### General

- `Esc` : exit the simulation

## Recordings

Simulation recordings are stored under folders such as:

```powershell
.\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\
```

These folders contain per-agent CSV trajectory files that can be used for visualization and metric computation.

## Visualizing Trajectories

Use `viz.py` to step through recorded trajectories:

```powershell
python .\viz.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\
```

Optional arguments:

- `--no-orientation` hides orientation vectors
- `--agent-radius <value>` changes the displayed marker size

Example:

```powershell
python .\viz.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\ --agent-radius 0.25
```

### Visualization Controls

- `Right Arrow` or `N` : step forward
- `Left Arrow` or `P` : step backward
- `T` : toggle trajectory trails
- `Home` : jump to the first frame
- `End` : jump to the last frame
- `Q` or `Esc` : close the viewer

## Robot Metrics

Use `robot_metrics.py` to compute metrics for one robot trajectory folder:

```powershell
python .\robot_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\
```

By default, the script computes metrics for agent `5`.

Metrics include:

- duration
- timeout status
- distance to goal
- average and maximum speed
- acceleration and jerk
- energy expenditure
- path length and extra distance ratio
- collisions
- safety
- average DCA
- average closest human distance

Useful options:

- `--agent-id <id>`: select the robot agent id
- `--robot-radius <value>`: set robot radius
- `--human-radius <value>`: set human radius
- `--goal-x <value>`: robot goal x position
- `--goal-y <value>`: robot goal y position
- `--goal-tolerance <value>`: success tolerance around the goal
- `--experiment-json <path>`: load human goals from an experiment JSON file
- `--human-goal-tolerance <value>`: goal tolerance for excluding humans from DCA

Example:

```powershell
python .\robot_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\ --agent-id 4 --goal-x 3.84 --goal-y -1.25
```

### Batch Mode

To compute metrics for every immediate subfolder inside a parent directory:

```powershell
python .\robot_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\ --batch
```

To write results to a custom CSV:

```powershell
python .\robot_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\ --batch --output-csv .\robot_metrics_summary.csv
```

## Crowd Metrics

Use `crowd_metrics.py` to compute human crowd statistics from a recording folder:

```powershell
python .\crowd_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\
```

Crowd metrics include:

- per-human duration
- per-human average and maximum speed
- path linearity
- minimum neighbor distance
- average minimum neighbor distance
- average DCA
- average minimum TTC
- safety

Useful options:

- `--human-ids 0,1,2,3,4`: choose which agents are treated as humans
- `--human-radius <value>`: set human radius
- `--tau-threshold <value>`: TTC threshold for safety
- `--robot-id <id>`: set the robot agent id
- `--robot-radius <value>`: set the robot radius

Example:

```powershell
python .\crowd_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\20260305_230024\ --human-ids 0,1,2,3,4,5,6,7,8,9 --robot-id 11
```

### Batch Mode

To compute crowd metrics for every immediate subfolder inside a parent directory:

```powershell
python .\crowd_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\ --batch
```

To write results to a custom CSV:

```powershell
python .\crowd_metrics.py .\Recordings\Trajectories\Empty\DiagonalORCA\ --batch --output-csv .\crowd_metrics_summary.csv
```

## Expected Recording Format

The analysis scripts expect a folder containing per-agent CSV files named like:

```text
output_0.csv
output_1.csv
output_2.csv
...
```

Each CSV row is expected to contain at least:

```text
time, x, y
```

and may optionally include orientation values:

```text
time, x, y, orientation_x, orientation_y
```
