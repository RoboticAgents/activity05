## Activity 5: ROS2 Environment Setup & First Simulation

This activity is a **hands-on supplement to the Week 10 ROS2 intro slides**: [ROS2 Intro Slides](https://learnroboticagents.com/slides/ros2intro.html).

You will set up the course ROS2 simulation environment and practice the same core concepts from lecture (nodes, topics, messages, publish/subscribe) by driving a TurtleBot3 in Gazebo.

**Due:** Monday, March 23 at 11:00 AM

---

## Learning Objectives

By completing this activity, you will:

- Launch the course Docker-based ROS2 environment on your laptop
- Run TurtleBot3 in Gazebo Harmonic (starting with `empty_world`)
- Drive the robot with the correct `TwistStamped` teleop command
- Inspect nodes/topics/messages with core ROS2 CLI tools
- Connect hands-on commands to the `ros2intro` slide concepts

---

## Part 1: Install Docker and Start the Environment

### 1.1 Install Docker Desktop

Install Docker Desktop for your OS:

- **Windows**: [Docker Desktop for Windows](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe) (requires WSL2 - Docker will prompt you to enable it)
- **macOS (Intel)**: [Docker Desktop for Mac (Intel)](https://desktop.docker.com/mac/main/amd64/Docker.dmg)
- **macOS (Apple Silicon / M1-M4)**: [Docker Desktop for Mac (Apple Silicon)](https://desktop.docker.com/mac/main/arm64/Docker.dmg)
- **Linux**: [Docker Desktop for Linux](https://docs.docker.com/desktop/install/linux-install/)

After installation, launch Docker Desktop and let it finish starting up.

> **Why Docker?** ROS2 runs natively on Ubuntu Linux. Docker gives everyone the same environment across Windows/macOS/Linux and avoids local package/version conflicts.

### 1.2 Launch the Environment

Open a terminal on your host machine and navigate to this folder:

```bash
cd activity05
```

Start the container:

```bash
docker compose up
```

**First time:** This pulls the pre-built image (~4 GB). You'll see download progress in your terminal. Wait for it to complete.

**Subsequent times:** Starts almost instantly because the image is cached.

### 1.3 Connect via Browser (noVNC)

Open your web browser and go to:

**[http://localhost:6080](http://localhost:6080)**

You should see a full Ubuntu desktop running inside your browser. Right-click the desktop and select **Terminal** to open a terminal.

---

## Part 2: Launch TurtleBot3 in Gazebo

In the VNC desktop (the browser window), **right-click** the desktop and select **Terminal** (or find LXTerminal in the application menu at the bottom).

Start in the low-complexity world first:

```bash
ros2 launch turtlebot3_gazebo empty_world.launch.py
```

Wait for Gazebo to fully load. The robot should be stationary in the center.

If the robot drifts while you're setting up teleop, pause Gazebo, then unpause when ready.

Once this works, try additional worlds:

```bash
# TurtleBot3 logo world
ros2 launch turtlebot3_gazebo turtlebot3_world.launch.py

# House world
ros2 launch turtlebot3_gazebo turtlebot3_house.launch.py
```

You should see:

- A **Gazebo window** with a simulated world
- A small circular robot (**TurtleBot3 Burger**) in the center
- Walls and obstacles around the robot

> **Performance note:** If Gazebo is laggy, use `empty_world.launch.py`, keep the browser window smaller, and see the **Troubleshooting** section for low-lag mode.

**Screenshot 1:** Take a screenshot of the Gazebo window showing the TurtleBot3 in the world.

---

## Part 3: Drive the Robot

**Right-click** the VNC desktop -> **Terminal** to open a second terminal window.

Launch keyboard teleoperation:

```bash
ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -p stamped:=true
```

You should see control instructions:

```text
Control Your TurtleBot3!
---------------------------
Moving around:
        i
   j    k    l
        ,

i/, : increase/decrease linear velocity
j/l : increase/decrease angular velocity
k : force stop
```

Drive the robot around:

- **`i`** - move forward
- **`j`** - turn left
- **`l`** - turn right
- **`,`** - move backward
- **`k`** - stop

Try navigating through gaps between obstacles without colliding.

**Screenshot 2:** Take a screenshot showing the robot in a different position after driving it around.

---

## Part 4: Explore ROS2

This section reiterates the `ros2intro` slide concepts: **nodes**, **topics**, **messages**, and **publish/subscribe**.

Open a **third terminal** in the VNC desktop.

### 4.1 List Active Nodes

```bash
ros2 node list
```

**Write down** the list of nodes you see. Nodes are individual processes that each handle one task (e.g., simulating the robot, processing sensor data).

### 4.2 List Active Topics

```bash
ros2 topic list
```

**Write down** the list of topics. Topics are named communication channels that nodes use to send and receive messages.

### 4.3 Inspect Sensor Data

View one message from the robot's simulated lidar sensor:

```bash
ros2 topic echo /scan --once
```

You'll see a `LaserScan` message with an array of distance values - these are what the robot "sees" around it.

### 4.4 Watch Velocity Commands

In this terminal, start echoing the velocity topic:

```bash
ros2 topic echo /cmd_vel
```

Now switch to the **teleop terminal** and press `i` or `j`. You should see velocity messages appear in real time in the echo terminal.

**Write down** one velocity message. What do `linear.x` and `angular.z` represent?

> **Hint:** `linear.x` = forward/backward speed (m/s). `angular.z` = rotation speed (rad/s).

---

## Verification Checklist

Before you're done, verify that you can answer **YES** to all of these:

- [ ] Docker Desktop is installed and running on my laptop
- [ ] `docker compose up` successfully starts the ROS2 environment
- [ ] I can access the VNC desktop at `http://localhost:6080`
- [ ] Gazebo launches with the TurtleBot3 in `empty_world`
- [ ] I can drive the robot with keyboard teleoperation
- [ ] `ros2 node list` shows active nodes
- [ ] `ros2 topic list` shows active topics
- [ ] I can echo sensor data from `/scan`

**Submit:** Fill out `reflection.md` in this folder with your two screenshots, command outputs, and short reflection.

---

## Key ROS2 Concepts

| Concept | What It Is |
|:---|:---|
| **ROS2** | Robot Operating System 2 - middleware that connects robot software components via a publish/subscribe messaging system |
| **Node** | A single process that performs one task (e.g., reading a sensor, controlling a motor, planning a path) |
| **Topic** | A named communication channel; nodes **publish** data to topics and **subscribe** to receive data |
| **Message** | Structured data sent over a topic (e.g., `TwistStamped` for velocity commands, `LaserScan` for lidar data) |
| **Gazebo** | A physics simulator that models robots, sensors, and environments |
| **TurtleBot3** | A small, affordable robot platform widely used in robotics education and research |
| **Teleoperation** | Controlling a robot manually (e.g., keyboard, joystick) rather than autonomously |

---

## Shutting Down

When you're done:

1. Press `Ctrl+C` in each VNC terminal to stop running commands
2. In your **host terminal** (not VNC), press `Ctrl+C` where `docker compose up` is running
3. To fully remove the container: `docker compose down`
4. The image stays cached - next time `docker compose up` starts instantly

---

## Troubleshooting

### Docker Daemon Not Running

If you see `failed to connect to the docker API ... docker.sock ... no such file or directory`:

1. Open Docker Desktop
2. Wait until it shows "Engine running"
3. Re-run `docker compose up`

### Port 6080 Already in Use

Edit `docker-compose.yml` and change `"6080:80"` to `"6081:80"`, then access `http://localhost:6081`.

### Low-Lag Mode

If Gazebo is slow, stop the container (`Ctrl+C`) and restart with the low-lag override file included in this folder:

```bash
docker compose -f docker-compose.yml -f compose.lowlag.yml up
```

This uses lower resolution (`1024x576`) and larger shared memory (`2gb`).

### Robot Does Not Move with Teleop

The most common cause is a message type mismatch. The Gazebo bridge expects `TwistStamped` on `/cmd_vel`.

1. Make sure you launched teleop with the `stamped` flag:

   ```bash
   ros2 run teleop_twist_keyboard teleop_twist_keyboard --ros-args -p stamped:=true
   ```

2. Verify `/cmd_vel` message types (both publisher and subscriber should show `geometry_msgs/msg/TwistStamped`):

   ```bash
   ros2 topic info /cmd_vel --verbose
   ```

3. Verify the bridge node exists:

   ```bash
   ros2 node list | grep bridge
   ```

   You should see a `parameter_bridge` node.

4. **Send a velocity command directly** to test whether the bridge and simulation are working, bypassing teleop entirely:

   ```bash
   ros2 topic pub --once /cmd_vel geometry_msgs/msg/TwistStamped \
     "{header: {stamp: {sec: 0, nanosec: 0}, frame_id: ''}, twist: {linear: {x: 0.2, y: 0.0, z: 0.0}, angular: {x: 0.0, y: 0.0, z: 0.0}}}"
   ```

   If the robot moves, the simulation is fine and the issue is with your teleop terminal (make sure it has keyboard focus).
