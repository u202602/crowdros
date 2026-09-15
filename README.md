## Download and Extract
# Download Here: https://github.com/u202602/crowdros/releases
The release packages include both Windows and Linux versions. Unzip the downloaded files and continue the rest of this README from inside the `Build` folder.





# CrowdROS: A Crowd Simulation Platform for Social Robot Navigation

CrowdROS is a simulation platform for building crowd navigation scenarios, running social robot experiments, visualizing recorded trajectories, and computing robot and crowd metrics from saved runs.

## Running the Simulator
Launch the simulator from the project root:

```powershell
.\build.exe
```

On Linux, run:

```bash
./build.x86_64
```

## Basic Workflow

1. Select a scene from the scene dropdown.
2. Create a scenario with the `Author Micro Scenario` or `Author Macro Scenario` button (see [Creating a Scenario](docs/scenario-authoring.md)). Note that some scenes (e.g. `Empty`) only support micro authoring.
3. Choose how to control the robot with the `Enable Keyboard` checkbox:
   - Enabled: control the robot manually with the keyboard.
   - Disabled: control the robot by publishing ROS 2 `cmd_vel` messages.
4. Press `Start` to run the selected scene and scenario.

## Documentation

- [Creating a Scenario](docs/scenario-authoring.md) — micro vs. macro authoring, crowd knobs, saved scenario format
- [Controls](docs/controls.md) — camera, robot, and simulation keybindings
- [ROS 2 Control](docs/ros2-integration.md) — installing ROS 2, building `ros_tcp_endpoint`, publishing `cmd_vel`, RViz
- [Analysis Tools](docs/analysis-tools.md) — `viz.py`, `robot_metrics.py`, `crowd_metrics.py`, `compare.py`, and the expected recording format
