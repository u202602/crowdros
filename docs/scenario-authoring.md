# Creating a Scenario

There are two ways to build a scenario, depending on how much control you want over individual agents. Scenarios can't be edited after they're saved, if one comes out wrong, author it again and save over it.

## Micro authoring: place every agent by hand

Use this when you need exact control over each agent.

1. From the scene selector, click `Author Micro Scenario`.
2. Place human agents and robot agents with left click, and their goals with right click. Placements must land inside the red-outlined spawnable area. Use the `Reset` button to clear the points on the map and start over.
3. After placing all agents and goals, click `Next`.
4. Configure each agent:
   - For humans: choose the agent type, crowd policy, and preferred speed (between 1 and 2 m/s).
   - For robots: choose the robot type.
5. Enter a scenario name and click `Save`.

## Macro authoring: describe a crowd with knobs

Use this when you want a crowd of a certain character (dense, hurried, grouped) without placing every person individually.

1. From the scene selector, click `Author Macro Scenario`.
2. Optionally add a route between the two blue boxes, or leave it entirely.
3. Set the seven crowd knobs, each between 0 and 1: `density`, `grouping`, `personalSpace`, `urgency`, `flowOrder`, `heterogeneity`, `loitering`. Optionally pin the crowd to a specific route instead of letting the knobs choose one, or pin the whole crowd to one steering policy.
4. Add a robot with a spawn point if you want one. A goal is optional, leave it out and the robot just sits there.
5. Set a random seed so the run is reproducible, name the scenario, and `Save`.

Every scenario is saved as JSON under `Scenarios/<Scene>/<name>.json`. Macro scenarios record their seed, so the same scenario and seed always regenerates the same crowd composition.
