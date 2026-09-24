# Workspace setup

## Prerequisites

These instructions target a machine with ROS 2 Jazzy installed at `/opt/ros/jazzy`, using Bash. Install ROS before continuing. The existing development machine has that ROS installation; a clean-machine setup has not yet been tested.

Install workspace tools and simulation dependencies:

```bash
sudo apt update
sudo apt install python3-colcon-common-extensions python3-vcstool python3-rosdep ros-jazzy-ros-gz ros-jazzy-teleop-twist-keyboard ros-jazzy-rviz2
```

If rosdep has never been initialized on this machine, run `sudo rosdep init` once. Then run `rosdep update`.

## Clone and import

For a fresh checkout:

```bash
git clone https://github.com/eileentyz/linorobot2.git
cd linorobot2
mkdir -p src
vcs import src < dependencies.repos
```

For the existing workspace, use `cd /home/eileen/moretea_ws/linorobot2`. The upstream clone already exists in `src/linorobot2`; keep it and skip the import. Check its revision with `git -C src/linorobot2 rev-parse HEAD` and compare with `dependencies.repos`.

## Install package dependencies and build

From the workspace root:

```bash
source /opt/ros/jazzy/setup.bash
rosdep install --from-paths src --ignore-src --rosdistro jazzy -y --skip-keys "microxrcedds_agent micro_ros_agent"
colcon build --symlink-install
source install/setup.bash
export LINOROBOT2_BASE=2wd
```

The skipped micro-ROS dependencies are for physical hardware. This guide covers simulation; hardware bringup needs the upstream hardware and sensor setup as well. Resolve any other rosdep errors before building.

## Check package discovery

```bash
ros2 pkg prefix linorobot2_gazebo
ros2 pkg prefix linorobot2_navigation
```

Continue with [simulation & mapping](simulation.md).

## Troubleshooting

| Symptom | Check |
| --- | --- |
| `ros2` or a package is not found | Source ROS and then `install/setup.bash` in that terminal. |
| URDF path contains `None` | Set `LINOROBOT2_BASE` before launching. |
| Simulator packages are missing | Install `ros-jazzy-ros-gz` and resolve rosdep errors. |
| Mapping does not update | Use `sim:=true`; check `/clock` and `/scan` with `ros2 topic list`. |
| Map fails to load | Pass an absolute YAML path and keep its referenced PGM beside it. |
| RViz is missing | Set `rviz:=true` on the SLAM or navigation launch command. |
