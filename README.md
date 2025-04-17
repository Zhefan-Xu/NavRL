# NavRL: Learning Safe Flight in Dynamic Environments
[![Python](https://img.shields.io/badge/python-3.10-4B8BBE.svg)](https://docs.python.org/3/whatsnew/3.10.html)
[![ROS1](https://img.shields.io/badge/ROS1-Noetic-green.svg)](https://wiki.ros.org/noetic)
[![ROS2](https://img.shields.io/badge/ROS2-Humble-F39C12.svg)](https://docs.ros.org/en/humble/index.html)
[![IsaacSim](https://img.shields.io/badge/IsaacSim-NVIDIA-C0392B.svg)](https://docs.omniverse.nvidia.com/isaacsim/latest/overview.html)
[![Linux platform](https://img.shields.io/badge/platform-Ubuntu-27AE60.svg)](https://releases.ubuntu.com/22.04/)



Welcome to the NavRL repository! This repository provides the implementation of the [NavRL](https://ieeexplore.ieee.org/document/10904341) framework, designed to enable robots to safely navigate dynamic environments using Reinforcement Learning. While the original paper focuses on UAV navigation, the NavRL can be extended to any robot that adopts a velocity-based control system.

<table>
  <tr>
    <td><img src="media/NavRL-demo1.gif" style="width: 100%;"></td>
    <td><img src="media/NavRL-demo2.gif" style="width: 100%;"></td>
    <td><img src="media/NavRL-demo3.gif" style="width: 100%;"></td>
  </tr>
</table>


For additional details, please refer to the related paper available here:

Zhefan Xu, Xinming Han, Haoyu Shen, Hanyu Jin, and Kenji Shimada, "NavRL: Learning Safe Flight in Dynamic Environments”, *IEEE Robotics and Automation Letters (RA-L)*, 2025. [\[IEEE Xplore\]](https://ieeexplore.ieee.org/document/10904341) [\[preprint\]](https://arxiv.org/pdf/2409.15634) [\[YouTube\]](https://youtu.be/EbeJW8-YlvI) [\[BiliBili\]](https://www.bilibili.com/video/BV1gsA9eTErz/?share_source=copy_web&vd_source=1333db331406abb1b5d4cece1e253427)


## News
* **2025-04-06:** We release easy-to-run Python scripts that allows users to quickly run demos.
* **2025-02-23:** The GitHub code, video demos, and relavant papers for our NavRL framework are released. The authors will actively maintain and update this repo!

## Table of Contents
 - [Training in NVIDIA Isaac Sim](#I-Training-in-NVIDIA-Isaac-Sim)
 - [Deployment Virtual Environment](#II-Deployment-Virtual-Environment)
 - [NavRL ROS1 Deployment](#III-NavRL-ROS1-Deployment)
 - [NavRL ROS2 Deployment](#IV-NavRL-ROS2-Deployment)
 - [Citation and Reference](#V-Citation-and-Reference)
 - [Acknowledgement](#VI-Acknowledgement)

## NavRL Quick Demos in 3 Minutes
We provide a pretrained model and easy-to-run Python scripts for quick demos of the NavRL framework.

<table>
  <tr>
    <td><img src="media/simple-navigation.gif" style="width: 100%;"></td>
    <td><img src="media/dynamic-navigation.gif" style="width: 100%;"></td>
    <td><img src="media/multi-robot-navigation.gif" style="width: 100%;"></td>
  </tr>
</table>

To get started, please follow the steps in [Deployment Virtual Environment](#II-Deployment-Virtual-Environment) to set up the Conda environment. Once the setup is complete, you can run the following three demos with the following commands:
```
conda activate NavRL
cd NavRL/quick-demos

# DEMO I: Navigating to a predefined goal point
python simple-navigation.py

# DEMO II: Navigating to dynamically/randomly assigned goal points
python random-navigation.py

# DEMO III: Multi-robot navigation
python multi-robot-navigation.py
```

## I. Training in NVIDIA Isaac Sim
This section provides the steps for training your own RL agent with the NavRL framework in Isaac Sim. **If you are not interested in training the agent yourself, feel free to skip this section and jump straight to the deployment section.**


### Isaac Sim Installation
This project was developed using **Isaac Sim version 2023.1.0-hotfix.1**, released in November 2023. **Please make sure you download and use this exact version, as using a different version may lead to errors due to version incompatibility.** Also, ensure that you have [conda](https://docs.anaconda.com/miniconda/) installed.

If you have already downloaded Isaac Sim version 2023.1.0-hotfix.1, you can skip the following steps. Otherwise, please follow the instructions below to download the legacy version of Isaac Sim, as the official installation does not support legacy version downloads. 

To download Isaac Sim version 2023.1.0-hotfix.1:

a. First, follow the steps on [this link](https://docs.omniverse.nvidia.com/isaacsim/latest/installation/install_container.html) to complete the Docker Container Setup. 

b. Then, download the Isaac Sim to your docker container:
```
docker pull nvcr.io/nvidia/isaac-sim:2023.1.0-hotfix.1

docker run --name isaac-sim --entrypoint bash -it --runtime=nvidia --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
    -e "PRIVACY_CONSENT=Y" \
    -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
    -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
    -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
    -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
    -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
    -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
    -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
    -v ~/docker/isaac-sim/documents:/root/Documents:rw \
    nvcr.io/nvidia/isaac-sim:2023.1.0-hotfix.1
```
c. Move the downloaded Isaac Sim from the docker container to your local machine:
```
bash docker ps # check your container ID in another terminal

# Replace <id_container> with the output from the previous command
docker cp <id_container>:isaac-sim/. /path/to/local/folder # absolute path
```


Isaac Sim version 2023.1.0-hotfix.1 is now installed on your local machine.

### NavRL Training Setup
To set up the NavRL framework, clone the repository and follow these steps (this process may take several minutes):
```
# Set the ISAACSIM_PATH environment variable
echo 'export ISAACSIM_PATH="path/to/isaac_sim-2023.1.0-hotfix.1"' >> ~/.bashrc

cd NavRL/isaac-training
bash setup.sh
```
After the setup completes, you should have created a virtual environment named ```NavRL```.

### Verify Installation and Run a Training Example
Use the default parameter to run a training example with 2 robots to verify installation.

```
# Activate NavRL virtual environment
conda activate NavRL

# Run a training example with default settings
python training/scripts/train.py
```
If the repo is installed correctly, you should be able to see the Isaac Sim window as shown below: 

![isaac-training-window](https://github.com/user-attachments/assets/14a4d8a8-e607-434f-af9d-42d0d945e8d7)


### Train your own RL agent
The training environment settings and hyerparameters can be found in ```NavRL/isaac-training/training/cfg```.

The following example demonstrates training with 1024 robots, 350 static obstacles, and 80 dynamic obstacles (an RTX 4090 is required). We recommend using [Wandb](https://wandb.ai/site/) to monitor your training and evaluation statistics.
```
python training/scripts/train.py headless=True env.num_envs=1024 env.num_obstacles=350 \
env_dyn.num_obstacles=80 wandb.mode=online
```
After training for a sufficient amount of time, you should observe the robots learning to avoid collisions:

https://github.com/user-attachments/assets/2294bd94-69b3-4ce8-8e91-0118cfae9bcd


## II. Deployment Virtual Environment
This section provides the minimum conda environment setup required to deploy ```NavRL``` (including running on a real robot). If you have already created the  ```NavRL``` conda environment during the [Isaac training step](#I-Training-in-NVIDIA-Isaac-Sim), you can skip this section. To create the conda environment, run the following commands:
```
cd NavRL/isaac-training
bash setup_deployment.sh
```
Once the setup is complete, a conda environment named ```NavRL``` should be created. You can verify the installation by activating the environment:
```
conda activate NavRL
```


## III. NavRL ROS1 Deployment
This section demonstrates an example of deploying NavRL with ROS1 and Gazebo using a quadcopter robot. Ensure that your system meets the following requirements:

- Ubuntu 20.04 LTS
- ROS1 Noetic

First, install dependencies and copy the ```ros1``` folder from this repository into your catkin workspace.
```
sudo apt-get install ros-noetic-mavros*

cp ros1 /path/to/catkin_ws/src
catkin_make
```
Then, set the environment vairable for Gazebo models.
```
echo 'source /path/to/ros1/uav_simulator/gazeboSetup.bash' >> ~/.bashrc
```
Finally, start the simulation and deploy NavRL navigation.
```
# Launch the gazebo simulator
roslaunch uav_simulator start.launch

# Start the perception and safety module
roslaunch navigation_runner safety_and_perception_sim.launch

# Run the navigation node
conda activate NavRL
rosrun navigation_runner navigation_node.py
```
A Gazebo window will display the environment while an RViz window presents the data. Use RViz's ```2D Nav Goal``` tool to set the navigation target, as shown in the video below (note: the default environment and settings might be different from the video):


https://github.com/user-attachments/assets/b7cc7e2e-c01d-4e44-87e3-97271a3aaa0f


To change the environment settings, review the launch file at ```ros1/uav_simulator/launch/start.launch```. The parameters for each module are located in ```ros1/navigation_runner/cfg/*.yaml``` configuration files.


## IV. NavRL ROS2 Deployment
This section demonstrates an example of deploying NavRL with ROS2 and Isaac Sim using a Unitree Go2 quadruped robot. Ensure that your system meets the following requirements:
- Ubuntu 22.04 LTS
- ROS2 Humble

Before get started, please install the simulator based on [this link](https://github.com/Zhefan-Xu/isaac-go2-ros2).

First, copy the ```ros2``` folder from this repository into your ros2 workspace.
```
cp ros2 /path/to/ros2_ws/src
colcon build --symlink-install
```
Then, start the simulation and deploy NavRL navigation.
```
# Launch Isaac Go2 simulator
conda activate isaaclab
cd /path/to/isaac-go2-ros2
python isaac-go2-ros2.py

# Start the perception and safety module
ros2 launch navigation_runner perception.launch.py
ros2 launch navigation_runner safe_action.launch.py # optional

# Turn on Rviz2 visualization
ros2 launch navigation_runner rviz.launch.py

# Run the navigation launch
conda activate NavRL
ros2 launch navigation_runner navigation.launch.py
```
An Isaac Sim window will display the environment while an RViz window presents the data. Use RViz's ```2D Nav Goal``` tool to set the navigation target. The navigation example is shown in the following video:


https://github.com/user-attachments/assets/4787f503-d8a3-4d7b-9d17-7057b2cff1eb


## V. Citation and Reference
If our work is useful to your research, please consider citing our paper.
```
@ARTICLE{NavRL,
  author={Xu, Zhefan and Han, Xinming and Shen, Haoyu and Jin, Hanyu and Shimada, Kenji},
  journal={IEEE Robotics and Automation Letters}, 
  title={NavRL: Learning Safe Flight in Dynamic Environments}, 
  year={2025},
  volume={10},
  number={4},
  pages={3668-3675},
  keywords={Navigation;Robots;Collision avoidance;Training;Safety;Vehicle dynamics;Heuristic algorithms;Detectors;Autonomous aerial vehicles;Learning systems;Aerial systems: Perception and autonomy;reinforcement learning;collision avoidance},
  doi={10.1109/LRA.2025.3546069}}
```

## VI. Acknowledgement
The authors would like to express their sincere gratitude to Professor Kenji Shimada for his great support and all CERLAB UAV team members who contribute to the development of this research.

The Isaac Sim training component of the NavRL framework is built upon [OmniDrones](https://github.com/btx0424/OmniDrones).

## Repository Architecture

NavRL仓库由几个关键模块组成，每个模块负责框架的不同方面。以下是主要模块及其功能的概述：

- **isaac-training**：该模块���含在NVIDIA Isaac Sim中训练RL代理的训练脚本和配置。它包括训练环境设置、训练脚本和不同训练场景的配置文件。
- **ros1**：该模块包含NavRL框架的ROS1实现。它包括在Gazebo仿真环境中部署NavRL框架所需的启动文件、配置文件和脚本。
- **ros2**：该模块包含NavRL框架的ROS2实现。它包括在Isaac Sim仿真环境中部署NavRL框架所需的启动文件、配置文件和脚本。
- **quick-demos**：该模块包含用于快速演示NavRL框架的易于运行的Python脚本。它包括简单导航、动态导航和多机器人导航的脚本。

## 数据处理和开发

NavRL框架通过几个阶段处理和开发数据：

1. **数据收集**：框架从各种传感器收集数据，包括LiDAR、摄像头和里程计传感器。这些数据用于感知环境和检测障碍物。
2. **数据预处理**：对收集的数据进行预处理，以去除噪声并提取相关特征。这包括过滤LiDAR点、检测摄像头图像中的物体以及估计机器人的姿态。
3. **数据集成**：将来自不同传感器的预处理数据集成在一起，以创建环境的综合表示。这包括融合LiDAR和摄像头数据以检测和跟踪动态障碍物。
4. **数据利用**：RL代理使用集成数据进行决策和导航。代理使用数据感知障碍物、规划路径并控制机器人的运动。

## 实现新算法

要在NavRL框架中实现新算法，请按照以下步骤操作：

1. **定义算法**：定义新算法及其组件，包括策略网络、价值网络和算法所需的任何其他模块。
2. **集成算法**：将新算法集成到现有框架中。这包括修改训练脚本、配置文件和任何其他相关组件以支持新算法。
3. **训练算法**：使用`isaac-training`模块中提供的训练脚本训练新算法。监控训练过程并根据需要调整超参数以达到预期性能。
4. **部署算法**：在ROS1或ROS2环境中部署训练好的算法。修改启动文件和配置文件以使用新算法进行导航。

## 使用ROS2将算法移植到其他机器人

要使用ROS2将NavRL算法移植到其他机器人，请按照以下步骤操作：

1. **机器人设置**：在ROS2环境中设置新机器人。这包括安装必要的ROS2软件包、配置机器人的传感器并确保可以通过ROS2控制机器人。
2. **修改配置文件**：修改`ros2`模块中的配置文件以匹配新机器人的规格。这包括更新传感器参数、机器人尺寸和任何其他相关设置。
3. **更新启动文件**：更新`ros2`模块中的启动文件以包含新机器人的启动配置。确保正确启动感知、安全和导航的必要节点。
4. **测试和调试**：在仿真环境中测试NavRL算法在新机器人上的运行。调试出现的问题并对配置文件和启动文件进行必要的调整。
5. **在真实机器人上部署**：一旦算法在仿真环境中正常工作，将其部署到真实机器人上。监控机器人的性能并进行最终调整以确保安全高效的导航。
# NavRL: Learning Safe Flight in Dynamic Environments
[![Python](https://img.shields.io/badge/python-3.10-4B8BBE.svg)](https://docs.python.org/3/whatsnew/3.10.html)
[![ROS1](https://img.shields.io/badge/ROS1-Noetic-green.svg)](https://wiki.ros.org/noetic)
[![ROS2](https://img.shields.io/badge/ROS2-Humble-F39C12.svg)](https://docs.ros.org/en/humble/index.html)
[![IsaacSim](https://img.shields.io/badge/IsaacSim-NVIDIA-C0392B.svg)](https://docs.omniverse.nvidia.com/isaacsim/latest/overview.html)
[![Linux platform](https://img.shields.io/badge/platform-Ubuntu-27AE60.svg)](https://releases.ubuntu.com/22.04/)



Welcome to the NavRL repository! This repository provides the implementation of the [NavRL](https://ieeexplore.ieee.org/document/10904341) framework, designed to enable robots to safely navigate dynamic environments using Reinforcement Learning. While the original paper focuses on UAV navigation, the NavRL can be extended to any robot that adopts a velocity-based control system.

<table>
  <tr>
    <td><img src="media/NavRL-demo1.gif" style="width: 100%;"></td>
    <td><img src="media/NavRL-demo2.gif" style="width: 100%;"></td>
    <td><img src="media/NavRL-demo3.gif" style="width: 100%;"></td>
  </tr>
</table>


For additional details, please refer to the related paper available here:

Zhefan Xu, Xinming Han, Haoyu Shen, Hanyu Jin, and Kenji Shimada, "NavRL: Learning Safe Flight in Dynamic Environments”, *IEEE Robotics and Automation Letters (RA-L)*, 2025. [\[IEEE Xplore\]](https://ieeexplore.ieee.org/document/10904341) [\[preprint\]](https://arxiv.org/pdf/2409.15634) [\[YouTube\]](https://youtu.be/EbeJW8-YlvI) [\[BiliBili\]](https://www.bilibili.com/video/BV1gsA9eTErz/?share_source=copy_web&vd_source=1333db331406abb1b5d4cece1e253427)


## News
* **2025-04-06:** We release easy-to-run Python scripts that allows users to quickly run demos.
* **2025-02-23:** The GitHub code, video demos, and relavant papers for our NavRL framework are released. The authors will actively maintain and update this repo!

## Table of Contents
 - [Training in NVIDIA Isaac Sim](#I-Training-in-NVIDIA-Isaac-Sim)
 - [Deployment Virtual Environment](#II-Deployment-Virtual-Environment)
 - [NavRL ROS1 Deployment](#III-NavRL-ROS1-Deployment)
 - [NavRL ROS2 Deployment](#IV-NavRL-ROS2-Deployment)
 - [Citation and Reference](#V-Citation-and-Reference)
 - [Acknowledgement](#VI-Acknowledgement)

## NavRL Quick Demos in 3 Minutes
We provide a pretrained model and easy-to-run Python scripts for quick demos of the NavRL framework.

<table>
  <tr>
    <td><img src="media/simple-navigation.gif" style="width: 100%;"></td>
    <td><img src="media/dynamic-navigation.gif" style="width: 100%;"></td>
    <td><img src="media/multi-robot-navigation.gif" style="width: 100%;"></td>
  </tr>
</table>

To get started, please follow the steps in [Deployment Virtual Environment](#II-Deployment-Virtual-Environment) to set up the Conda environment. Once the setup is complete, you can run the following three demos with the following commands:
```
conda activate NavRL
cd NavRL/quick-demos

# DEMO I: Navigating to a predefined goal point
python simple-navigation.py

# DEMO II: Navigating to dynamically/randomly assigned goal points
python random-navigation.py

# DEMO III: Multi-robot navigation
python multi-robot-navigation.py
```

## I. Training in NVIDIA Isaac Sim
This section provides the steps for training your own RL agent with the NavRL framework in Isaac Sim. **If you are not interested in training the agent yourself, feel free to skip this section and jump straight to the deployment section.**


### Isaac Sim Installation
This project was developed using **Isaac Sim version 2023.1.0-hotfix.1**, released in November 2023. **Please make sure you download and use this exact version, as using a different version may lead to errors due to version incompatibility.** Also, ensure that you have [conda](https://docs.anaconda.com/miniconda/) installed.

If you have already downloaded Isaac Sim version 2023.1.0-hotfix.1, you can skip the following steps. Otherwise, please follow the instructions below to download the legacy version of Isaac Sim, as the official installation does not support legacy version downloads. 

To download Isaac Sim version 2023.1.0-hotfix.1:

a. First, follow the steps on [this link](https://docs.omniverse.nvidia.com/isaacsim/latest/installation/install_container.html) to complete the Docker Container Setup. 

b. Then, download the Isaac Sim to your docker container:
```
docker pull nvcr.io/nvidia/isaac-sim:2023.1.0-hotfix.1

docker run --name isaac-sim --entrypoint bash -it --runtime=nvidia --gpus all -e "ACCEPT_EULA=Y" --rm --network=host \
    -e "PRIVACY_CONSENT=Y" \
    -v ~/docker/isaac-sim/cache/kit:/isaac-sim/kit/cache:rw \
    -v ~/docker/isaac-sim/cache/ov:/root/.cache/ov:rw \
    -v ~/docker/isaac-sim/cache/pip:/root/.cache/pip:rw \
    -v ~/docker/isaac-sim/cache/glcache:/root/.cache/nvidia/GLCache:rw \
    -v ~/docker/isaac-sim/cache/computecache:/root/.nv/ComputeCache:rw \
    -v ~/docker/isaac-sim/logs:/root/.nvidia-omniverse/logs:rw \
    -v ~/docker/isaac-sim/data:/root/.local/share/ov/data:rw \
    -v ~/docker/isaac-sim/documents:/root/Documents:rw \
    nvcr.io/nvidia/isaac-sim:2023.1.0-hotfix.1
```
c. Move the downloaded Isaac Sim from the docker container to your local machine:
```
bash docker ps # check your container ID in another terminal

# Replace <id_container> with the output from the previous command
docker cp <id_container>:isaac-sim/. /path/to/local/folder # absolute path
```


Isaac Sim version 2023.1.0-hotfix.1 is now installed on your local machine.

### NavRL Training Setup
To set up the NavRL framework, clone the repository and follow these steps (this process may take several minutes):
```
# Set the ISAACSIM_PATH environment variable
echo 'export ISAACSIM_PATH="path/to/isaac_sim-2023.1.0-hotfix.1"' >> ~/.bashrc

cd NavRL/isaac-training
bash setup.sh
```
After the setup completes, you should have created a virtual environment named ```NavRL```.

### Verify Installation and Run a Training Example
Use the default parameter to run a training example with 2 robots to verify installation.

```
# Activate NavRL virtual environment
conda activate NavRL

# Run a training example with default settings
python training/scripts/train.py
```
If the repo is installed correctly, you should be able to see the Isaac Sim window as shown below: 

![isaac-training-window](https://github.com/user-attachments/assets/14a4d8a8-e607-434f-af9d-42d0d945e8d7)


### Train your own RL agent
The training environment settings and hyerparameters can be found in ```NavRL/isaac-training/training/cfg```.

The following example demonstrates training with 1024 robots, 350 static obstacles, and 80 dynamic obstacles (an RTX 4090 is required). We recommend using [Wandb](https://wandb.ai/site/) to monitor your training and evaluation statistics.
```
python training/scripts/train.py headless=True env.num_envs=1024 env.num_obstacles=350 \
env_dyn.num_obstacles=80 wandb.mode=online
```
After training for a sufficient amount of time, you should observe the robots learning to avoid collisions:

https://github.com/user-attachments/assets/2294bd94-69b3-4ce8-8e91-0118cfae9bcd
4. `ros2`：该模块包含ROS2和Isaac Sim的配置文件和脚本，用于在仿真环境中部署NavRL。

## VIII. 数据处理与开发
本节将介绍仓库中数据的处理和开发方式。

### 数据处理
NavRL框架中的数据处理主要包括以下几个方面：
1. 传感器数据采集：通过传感器（如激光雷达、相机等）采集环境数据。
2. 数据预处理：对采集到的数据进行预处理，如滤波、去噪等。
## IX. 新算法的实现
本节将介绍如何在仓库中实现一个新算法。

### 新算法实现步骤
1. 确定算法目标：明确新算法的目标和功能。
2. 编写算法代码：在相应的模块中编写算法代码。
3. 测试算法：在仿真环境中测试新算法，确保其功能正常。
3. 配置ROS2节点：根据目标机器人的硬件配置，修改ROS2节点的配置文件。
4. 部署NavRL：将NavRL框架部署到目标机器人上，并进行测试。

通过以上步骤，您可以将NavRL算法成功移植到其他机器人上，并在ROS2环境中运行。

