# Analysis Tools

Scripts for visualizing recorded trajectories and computing robot and crowd metrics from saved runs.

## Recordings

Simulation recordings are stored under folders such as:

```powershell
./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944
```

These folders contain per-agent CSV trajectory files that can be used for visualization and metric computation.

## Python Requirements

The scripts below (`viz.py`, `robot_metrics.py`, `crowd_metrics.py`, `compare.py`) need Python 3.10+.

- `robot_metrics.py` and `crowd_metrics.py` use only the standard library, no packages to install.
- `viz.py` and `compare.py` need `matplotlib`, and `viz.py` also needs `numpy`:

```bash
pip install matplotlib numpy
```

## Visualizing Trajectories

Use `viz.py` to step through recorded trajectories:

```powershell
python viz.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944
```

Optional arguments:

- `--no-orientation` hides orientation vectors
- `--agent-radius <value>` changes the displayed marker size

Example:

```powershell
python viz.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944 --agent-radius 0.25
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
python robot_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944
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

- `--scenario <name or path>`: load the robot's agent id and goal position, and the human goal positions (for DCA), straight from the scenario file that was used to run this recording, instead of setting them by hand. Accepts a bare name (`Empty/KDMACrowd`), which is resolved against the `Scenarios` folder next to `Recordings`, or a full path to a scenario `.json`. Overridden by `--agent-id` / `--goal-x` / `--goal-y` if those are also given.
  - Only works for **micro-authored** scenarios (an explicit agent list). Macro-authored scenarios describe the crowd with knobs instead of per-agent start/goal points, so there's nothing for `--scenario` to read, use `--agent-id` / `--goal-x` / `--goal-y` / `--human-ids` / `--robot-id` by hand for those.
  - If an agent has more than one goal (a multi-leg journey), only its **first** goal is used, the metrics don't currently account for a robot or human re-routing mid-run.
- `--agent-id <id>`: select the robot agent id
- `--robot-radius <value>`: set robot radius
- `--human-radius <value>`: set human radius
- `--goal-x <value>`: robot goal x position
- `--goal-y <value>`: robot goal y position
- `--goal-tolerance <value>`: success tolerance around the goal
- `--human-goal-tolerance <value>`: goal tolerance for excluding humans from DCA

Example:

```powershell
python robot_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944 --agent-id 4 --goal-x 3.84 --goal-y -1.25
```

Or, with a scenario file instead of setting the agent id and goal by hand:

```powershell
python robot_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944 --scenario ./Scenarios/Empty/KDMACrowd
```

### Batch Mode

To compute metrics for every immediate subfolder inside a parent directory:

```powershell
python robot_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd --batch
```

To write results to a custom CSV:

```powershell
python robot_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd --batch --output-csv .\robot_metrics_summary.csv
```

## Crowd Metrics

Use `crowd_metrics.py` to compute human crowd statistics from a recording folder:

```powershell
python crowd_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944
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

- `--scenario <name or path>`: load human ids, robot id, and human start/goal positions straight from the scenario file that was used to run this recording, instead of `--human-ids` / `--robot-id` / the built-in defaults. Same name-or-path resolution as `robot_metrics.py` above. Overridden by `--human-ids` / `--robot-id` if those are also given.
  - Only works for **micro-authored** scenarios (an explicit agent list). Macro-authored scenarios describe the crowd with knobs instead of per-agent start/goal points, so there's nothing for `--scenario` to read, use `--human-ids` / `--robot-id` by hand for those.
  - If a human has more than one goal (a multi-leg journey), only its **first** goal is used for path linearity and DCA exclusion.
- `--human-ids 0,1,2,3,4`: choose which agents are treated as humans
- `--human-radius <value>`: set human radius
- `--tau-threshold <value>`: TTC threshold for safety
- `--robot-id <id>`: set the robot agent id
- `--robot-radius <value>`: set the robot radius

Example:

```powershell
python crowd_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944 --human-ids 0,1,2,3,4,5,6,7,8,9 --robot-id 11
```

Or, with a scenario file instead of setting ids by hand:

```powershell
python crowd_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd/20260911_162944 --scenario ./Scenarios/Empty/KDMACrowd
```

### Batch Mode

To compute crowd metrics for every immediate subfolder inside a parent directory:

```powershell
python crowd_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd --batch
```

To write results to a custom CSV:

```powershell
python crowd_metrics.py ./Recordings/Trajectories/Empty/KDMACrowd --batch --output-csv .\crowd_metrics_summary.csv
```

## Comparing Policies

Use `compare.py` to compare two (or more) robot policies run on the same scenario, e.g. Arena vs. DRL-VO:

```powershell
python compare.py --scenario ./Scenarios/Plaza\simple `
    Trajectories\Plaza\simple\arena Trajectories\Plaza\simple\drlvo
```

A bare folder uses its own name as the policy label. To choose the labels explicitly, pass `name=path` pairs instead:

```powershell
python compare.py --scenario ./Scenarios/Plaza\simple `
    arena=Trajectories\Plaza\simple\arena drlvo=Trajectories\Plaza\simple\drlvo
```

`--scenario` is required, it's how the script finds the robot's id and goal and the human goals for every run being compared. Like the metrics scripts above, this only works for **micro-authored** scenarios, and only reads each agent's first goal if it has more than one.

It prints a summary table and writes four plots to `--output-dir` (default `compare_plots/`):

- `trajectories.png`: overhead view of each policy's robot path
- `crowd_trajectories.png`: overhead view of every human's path per policy, side by side
- `robot_comparison.png`: bar chart of time to goal
- `crowd_comparison.png`: bar chart of average human speed

Other useful options:

- `--robot-radius <value>` / `--human-radius <value>`: agent radii
- `--tau-threshold <value>`: TTC threshold for safety
- `--goal-tolerance <value>`: success tolerance around the goal
- `--output-dir <path>`: where to write the plots
- `--show`: also pop up one window with all four plots tiled together

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
