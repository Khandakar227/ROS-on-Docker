## Basic ROS 2 Commands

### Workspace and Build Commands
`colcon build`: Builds packages in the workspace. The `--symlink-install` option changes how files are installed after the build process, creating symbolic links (symlinks) instead of copying files to the install directory. 

`colcon clean`: Cleans the build, install, and log directories.

`colcon list`: Lists all packages in the workspace.

`colcon test`: Runs tests for the packages in the workspace.

### Environment Setup
`source /opt/ros/humble/setup.bash`: Sources the ROS 2 Humble environment.

`source install/local_setup.bash`: Sources the local workspace setup.

### Create a ROS 2 Package
```bash
ros2 pkg create <package_name> --build-type <build_type> --dependencies <dependencies>
```

`<package_name>`: The name of the package you want to create.

`<build_type>`: The build system to use (e.g., ament_cmake, ament_python, cmake).

`<dependencies>`: A list of ROS 2 packages or other dependencies required by your package.

#### Build Type

`--build-type ament_cmake`: For C++ packages (uses CMake).

`--build-type ament_python`: For Python packages.

`--build-type cmake`: For plain CMake packages (non-ROS).

### Package Management

`ros2 pkg list`: Lists all installed ROS 2 packages.

`ros2 pkg executables <package_name>`: Lists executables in a specific package.

`ros2 pkg prefix <package_name>`: Shows the installation prefix of a package.

### Node and Topic Commands

`ros2 run <package_name> <executable_name>`: Runs a specific node from a package.

`ros2 node list`: Lists all running nodes.

`ros2 node info <node_name>`: Provides detailed information about a specific node.

`ros2 topic list`: Lists all active topics.

`ros2 topic echo <topic_name>`: Displays messages published to a topic.

`ros2 topic info <topic_name>`: Provides information about a specific topic.

`ros2 topic pub <topic_name> <msg_type> <data>`: Publishes a message to a topic.

### Service Commands

`ros2 service list`: Lists all available services.

`ros2 service call <service_name> <service_type> <arguments>`: Calls a service with the provided arguments.

`ros2 service type <service_name>`: Displays the type of a specific service.

### Action Commands

`ros2 action list`: Lists all available actions.

`ros2 action info <action_name>`: Provides information about a specific action.

`ros2 action send_goal <action_name> <action_type> <arguments>`: Sends a goal to an action server.

### Parameter Commands

`ros2 param list`: Lists all parameters for a node.

`ros2 param get <node_name> <parameter_name>`: Retrieves the value of a parameter.

`ros2 param set <node_name> <parameter_name> <value>`: Sets the value of a parameter.

`ros2 param dump <node_name>`: Dumps all parameters of a node to a YAML file.

`ros2 param load <node_name> <file_path>`: Loads parameters from a YAML file.

### Launch Commands

`ros2 launch <package_name> <launch_file>`: Launches a ROS 2 launch file.

`ros2 launch <package_name> <launch_file> <arguments>`: Launches a ROS 2 launch file with arguments.

### Bagging and Playback

`ros2 bag record <topic_names>`: Records messages from specified topics into a bag file.

`ros2 bag play <bag_file>`: Plays back messages from a bag file.

`ros2 bag info <bag_file>`: Displays information about a bag file.

### Interface Commands

`ros2 interface list`: Lists all available message, service, and action interfaces.

`ros2 interface show <interface_name>`: Displays the definition of a specific interface.

### Miscellaneous Commands

`ros2 doctor`: Checks the ROS 2 setup for potential issues.

`ros2 component list`: Lists all loaded components.

`ros2 component load <container_node> <plugin_name>`: Loads a component into a container node.
