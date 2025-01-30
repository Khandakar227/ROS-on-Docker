# Docker Compose Configuration for ROS 2 Humble Development

## Overview

This **Docker Compose** file sets up a **ROS 2 Humble** development environment using the `osrf/ros:humble-desktop-full` image. It enables seamless development by forwarding graphical applications, mounting a workspace, and providing access to hardware devices.This will also work for other versions of ROS. Just change `image`.

## Prerequisites

Before using this Compose file, ensure you have:

- **Docker** installed on your system ([Installation Guide](https://docs.docker.com/get-docker/)).
- **X11 forwarding** configured if you want to run GUI-based ROS applications.

## How to Use

### 1. Clone Your ROS 2 Workspace (If Not Available)

Ensure you have a **ROS 2 workspace** on your host machine at `~/ros2_ws`. If you don’t have one, create it:

```bash
mkdir -p ~/ros2_ws/src
cd ~/ros2_ws
colcon build
```

### 2. Run the Container

Navigate to the directory containing this `docker-compose.yml` file and start the container with:

```bash
docker compose up -d
```

This runs the container in detached mode (`-d`), meaning it will run in the background.

### 3. Access the Container

To enter the running container:

```bash
docker exec -it ros2_humble_dev bash
```

### 4. Stop the Container

To stop and remove the container:

```bash
docker compose down
```

## Configuration Breakdown

### **Service Definition**

```yaml
services:
  ros2_humble:
```

Defines a service named **ros2\_humble**, which will create a container based on the provided configurations.

### **Docker Image**

```yaml
image: osrf/ros:humble-desktop-full
```

Specifies the official **ROS 2 Humble Desktop Full** image, which includes ROS tools, GUI applications, and development utilities.

### **Container Name**

```yaml
container_name: ros2_humble_dev
```

Assigns a fixed name (`ros2_humble_dev`) to the container for easy identification.

### **Privileges and Environment Variables**

```yaml
privileged: true
```

Grants elevated privileges to the container, allowing access to hardware devices.

```yaml
environment:
  - DISPLAY=${DISPLAY}
  - QT_X11_NO_MITSHM=1
  - USER=${USER}
  - UID=${UID}
  - GID=${GID}
```

- **`DISPLAY=${DISPLAY}`**: Enables GUI applications inside the container.
- **`QT_X11_NO_MITSHM=1`**: Prevents shared memory issues with Qt applications.
- **`USER=${USER}`**: Ensures that graphical applications run under the same user as the host.
- **`UID`**** & ****`GID`**: Match the user ID and group ID with the host system to avoid permission issues.

### **Volume Mounts**

```yaml
volumes:
  - ~/ros2_ws:/home/ros2_ws
  - /dev:/dev
  - /tmp/.X11-unix:/tmp/.X11-unix
```

- **`~/ros2_ws:/home/ros2_ws`**: Mounts the host’s ROS 2 workspace into the container.
- **`/dev:/dev`**: Allows access to hardware devices (e.g., sensors, actuators).
- **`/tmp/.X11-unix:/tmp/.X11-unix`**: Enables X11 forwarding for running GUI applications.

### **Network Mode**

```yaml
network_mode: host
```

Uses the **host network** to simplify communication between ROS nodes running in the container and on the host.

### **Working Directory**

```yaml
working_dir: /home/ros2_ws
```

Sets the default working directory to `/home/ros2_ws`, the mounted ROS 2 workspace.

### **Terminal and Restart Policies**

```yaml
tty: true
stdin_open: true
restart: unless-stopped
```

- **`tty: true`**** & ****`stdin_open: true`**: Keep an interactive terminal open.
- **`restart: unless-stopped`**: Ensures the container restarts automatically unless explicitly stopped.

## Additional Notes

- If you face **permission issues**, run:
  ```bash
  xhost +local:
  ```
- If you modify `docker-compose.yml`, apply changes with:
  ```bash
  docker compose down && docker compose up -d
  ```
- For ROS 2 package development, build your workspace inside the container:
  ```bash
  colcon build --symlink-install
  ```

## Troubleshooting

- **Container fails to start?** Run:
  ```bash
  docker logs ros2_humble_dev
  ```

- **X11 forwarding not working?** Ensure `xhost` is configured correctly (Important for running GUI applications):
  ```bash
  xhost +local:
  ```

- **Permission issues with workspace?** Change ownership:
  ```bash
  sudo chown -R $USER:$USER ~/ros2_ws
  ```

---