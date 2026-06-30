
**What is a physics simulation at its core?**

A computer simulation of the physical world is a program that models physical laws mathematically and updates the state of objects over time. At every time step it calculates: where is each object, what forces are acting on it, and where will it be at the next time step.

The core things it simulates:

- **Rigid body dynamics** — objects have mass, position, velocity, orientation. Forces like gravity act on them.
- **Collision detection** — are two objects touching?
- **Collision response** — what happens when they touch? Do they bounce, slide, stop?

That's the foundation of any physics engine.

**What is Gazebo specifically?**

Gazebo is a physics simulator built for robotics. It uses an underlying physics engine (by default Bullet or ODE) to simulate the physical world, and adds robotics-specific features on top:

- Sensor simulation — cameras, LIDAR, IMU, GPS
- Robot model loading from description files
- ROS2 integration so your nodes can receive sensor data from and send commands to the simulated robot

Think of it as: physics engine + robotics sensors + ROS2 bridge.

**Why do we need it?**

Your rescue robot needs to navigate a physical world, avoid obstacles, and use sensors. Without Gazebo you'd need real hardware to test anything. Gazebo lets you develop and test entirely in simulation.

