# Simulation, mapping, and navigation

Run these commands from the workspace root. Prepare **every new terminal**:

```bash
source /opt/ros/jazzy/setup.bash
source install/setup.bash
export LINOROBOT2_BASE=2wd
```

## 1. Start the simulator

```bash
ros2 launch linorobot2_gazebo gazebo.launch.py
```

The default world is `turtlebot3_world`. To run without the Gazebo client, add `gui:=false`.

## 2. Create a map

In another terminal:

```bash
ros2 launch linorobot2_navigation slam.launch.py sim:=true rviz:=true
```

In a third terminal, drive the robot around the environment:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard
```

Keep keyboard focus on the teleop terminal. Follow its displayed keys and move slowly while checking the map in RViz.

## 3. Save a map

From a prepared terminal at the workspace root:

```bash
ros2 run nav2_map_server map_saver_cli -f "$PWD/maps/new_test_map" --ros-args -p save_map_timeout:=10000.0
```

Choose a new name for each experiment to preserve previous results. Commit both the YAML and its image. See [map notes](../maps/README.md).

## 4. Navigate on a saved map

Stop the SLAM launch with Ctrl+C before starting navigation; the SLAM launch also starts Nav2. Keep Gazebo running.

For the default simulated world, use the matching upstream default map:

```bash
ros2 launch linorobot2_navigation navigation.launch.py sim:=true rviz:=true
```

To use the saved Moretea map instead:

```bash
ros2 launch linorobot2_navigation navigation.launch.py sim:=true rviz:=true map:="$PWD/maps/moretea_test_map.yaml"
```

Use that map only with the environment it represents. Its original world and capture conditions have not yet been recorded; do not assume it matches the default world.

In RViz, use **2D Pose Estimate** to initialize the robot at its actual location, then use **Nav2 Goal** to choose a destination. Check that the laser scan aligns with the map before sending a goal.

## Record an experiment

For each run, note the date, upstream revision, base type, world, map, commands, expected result, and observed result. Add a screenshot or short recording after a successful run. Record failures as well so the documentation reflects what has actually been tested.
