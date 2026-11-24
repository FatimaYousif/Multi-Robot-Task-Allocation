# Multi-Robot Task Allocation

This project is a simulation (using ROS2) of five TurtleBot3 robots in a Gazebo warehouse. Tasks are allocated dynamically with the Hungarian algorithm, using real A* path lengths as costs, and each robot follows its assigned route with a PD controller.

## How it works

1. **Localization:** one AMCL instance per robot on a shared warehouse map.
2. **Costmap:** `inflation_costmap` inflates obstacles from the map for safer planning.
3. **Path planning:** an A* planner exposes a `PlanPath` service that returns a path and its length.
4. **Task allocation:** `path_allocation` reads robot poses from TF, queries A* for path costs, and runs a Hungarian-based assignment. It considers robot capabilities, battery, and task priority and service time. Each robot gets one concatenated path (robot → T1 → T2 → …) on `/tb3_N/path`.
5. **Motion control:** a PD path follower per robot publishes to `/tb3_N/cmd_vel`.

## Future Work

- **Robot-robot collision avoidance:** robots only avoid static obstacles today. Next step is a local planner (e.g. ORCA) or treating other robots as dynamic obstacles.
- **Faster task allocation:** running A* for every robot-task pair gets expensive as the fleet grows. Cheap distance estimates first, better caching, or auction-based methods (e.g. CBBA) would help.
