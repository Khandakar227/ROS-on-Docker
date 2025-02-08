# Docker Compose Configuration for ROS 2 Humble Development on Windows

## Overview

This **Docker Compose** file sets up a **ROS 2 Humble** development environment using the `osrf/ros:humble-desktop-full` image. It enables seamless development by forwarding graphical applications and providing GPU support on Windows through WSL2 and VcXsrv.

## Prerequisites

Before using this Compose file, ensure you have:

1. **Docker Desktop** installed with WSL2 backend ([Installation Guide](https://docs.docker.com/desktop/windows/install/))
2. **VcXsrv Windows X Server** installed ([Download VcXsrv](https://sourceforge.net/projects/vcxsrv/))
3. **WSL2** installed and configured ([Microsoft WSL2 Guide](https://learn.microsoft.com/en-us/windows/wsl/install))
4. (Optional) **NVIDIA GPU Driver** and **NVIDIA Container Toolkit** if using GPU acceleration

## Configuration Breakdown

### **Service Definition**

```yaml
services:
  ros2_humble:
```

Defines a service named **ros2_humble**, which will create a container based on the provided configurations.

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
  - DISPLAY=host.docker.internal:0.0
  - LIBGL_ALWAYS_INDIRECT=1
  - QT_X11_NO_MITSHM=1
  - NVIDIA_VISIBLE_DEVICES=all
  - NVIDIA_DRIVER_CAPABILITIES=all
```

- **`DISPLAY=host.docker.internal:0.0`**: Enables GUI applications inside the container using VcXsrv
- **`LIBGL_ALWAYS_INDIRECT=1`**: Ensures proper OpenGL rendering through X11 forwarding
- **`QT_X11_NO_MITSHM=1`**: Prevents shared memory issues with Qt applications
- **`NVIDIA_VISIBLE_DEVICES/CAPABILITIES`**: Enables GPU support (if NVIDIA GPU is present)

### **Volume Mounts**

```yaml
volumes:
  - ros2_workspace:/home/ros2_ws
  - /tmp/.X11-unix:/tmp/.X11-unix
```

- **`ros2_workspace:/home/ros2_ws`**: Creates and mounts a Docker volume for the ROS 2 workspace
- **`/tmp/.X11-unix:/tmp/.X11-unix`**: Enables X11 forwarding for running GUI applications

### **Network Mode**

```yaml
network_mode: bridge
```

Uses the **bridge network** as host network mode is not supported on Windows.

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

- **`tty: true` & `stdin_open: true`**: Keep an interactive terminal open
- **`restart: unless-stopped`**: Ensures the container restarts automatically unless explicitly stopped

## Setting Up X11 Forwarding

1. Launch VcXsrv (XLaunch) with these settings:

   - Display settings: Multiple windows
   - Display number: 0
   - Start no client
   - Extra settings:
     - ✓ Disable access control
     - ✓ Native opengl
     - ✓ Clipboard
     - ✓ Primary Selection

2. Before running the container, execute this PowerShell command

   ```powershell
   Set-Variable -Name DISPLAY -Value "host.docker.internal:0.0"
   ```

## Setting Up NVIDIA Support (Optional)

If you have an NVIDIA GPU and want to use it with ROS:

1. Install NVIDIA GPU Driver for Windows
2. Setup NVIDIA Container Toolkit in WSL2:

```powershell
# In PowerShell as Administrator
wsl --install
wsl --update
wsl --set-default-version 2

# In WSL2 Ubuntu terminal
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg
curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

## How to Use

1. Start VcXsrv with the configuration described above

2. Start the container:

Navigate to the directory containing this `compose.yaml` file and start the container with:

```powershell
docker compose up -d
```

3. Access the container:

```powershell
docker exec -it ros2_humble_dev bash
```

4. Stop the container:

```powershell
docker compose down
```

## Configure ROS 2 Environment

Configure environment inside the container using ROS2 - Humble docs [ROS 2 Environment Setup](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Configuring-ROS2-Environment.html).

## Using GUI Applications

Using turtlesim, ros2, and rqt in container from Humble docs [Using GUI Applications](https://docs.ros.org/en/humble/Tutorials/Beginner-CLI-Tools/Introducing-Turtlesim/Introducing-Turtlesim.html).

## Troubleshooting

- **Container fails to start?** Check logs:

```powershell
docker logs ros2_humble_dev
```

- **GUI applications not working?**

  1. Ensure VcXsrv is running with correct settings
  2. Verify DISPLAY environment variable in compose file
  3. Check if LIBGL_ALWAYS_INDIRECT is set to 1

- **NVIDIA GPU not detected?**
  1. Verify NVIDIA driver installation:
  ```powershell
  wsl nvidia-smi
  ```
  2. Check GPU access in container:
  ```powershell
  docker exec ros2_humble_dev nvidia-smi
  ```
