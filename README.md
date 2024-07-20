# ros2_maze-solver_setup
A custom ros2 maze solver package using turtlebbot3 model and a custom maze.

### 1. Pre-setup requirements
- Ubuntu 22.04 LTS
- ROS2 humble
- git
- python3.10 or plus

### 2. Check setup requirements

- checking your ros distribution : 
```
echo $ROS_DISTRO
```
It should output `humble`.

- checking your gazebo version (we need gazebo classic 11):
```
gazebo --version
```
It should output `version 11.xx.x`.

- check python3 version :
```
python3 --version
```
It should output `version 3.10.xx`.

- check git version : 
```
git --version
```
It should output `version 2.34.xx`.

Note : in case git or gazebo is not installed on your system, run the following commands.

```
sudo apt install ros-humble-gazebo-*
sudo apt install git
```

### 3. Create a ros2 workspace & source it in your `.bashrc` file :
```
cd ~
mkdir -p ros2_ws/src
cd ros2_ws
colcon build
echo "source ~/ros2_ws/install/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
Now you will see `build`, `log` & `devel` folders inside your `ros2_ws` directory.

### 4. Create a custom ros2 python type package name `maze_solver` : 
```
cd ~/ros2_ws/src
ros2 pkg create --build-type ament_python maze_solver --dependencies rclpy
cd ..
colcon build
source ~/.bashrc
```
This will create setup.cfg, setup.py, src/, package.xml, test/, resource/, maze_solver/ files & folders.

### 5. Add models and worlds folder in your `maze_solver` package :

-  Copy the `models` folder from this git repo to the `ros2_ws/src/maze_solver` directory.
-  Copy the `worlds` folder from this git repo to the `ros2_ws/src/maze_solver` directory.
- Also copy the content inside `models` folder & paste it to `/.gazebo/models` folder.    

Note : You could also give your maze_solver/models path to your GAZEBO_MODEL_PATH environment variable, but let's do it wihout adding models folder to GAZEBO_MODEL_PATH explicitly.    

### 6. Now modify your `maze_solver/setup.py` file with this code : 
```
from setuptools import find_packages, setup
import os
from glob import glob

package_name = 'maze_solver'

setup(
    name=package_name,
    version='0.0.0',
    packages=find_packages(),
    data_files=[
        ('share/ament_index/resource_index/packages',
            ['resource/' + package_name]),
        ('share/' + package_name, ['package.xml']),
        (os.path.join('share', package_name, 'worlds'), glob('worlds/*.world')),
    ],
    install_requires=['setuptools'],
    zip_safe=True,
    maintainer='mohit',
    maintainer_email='mkgautam31102002@gmail.com',
    description='TODO: Package description',
    license='Apache-2.0',
    tests_require=['pytest'],
    entry_points={
        'console_scripts': [
            'launch_maze_world = maze_solver.launch_maze_world:main',
        ],
    },
)
```
As your saw, we explicitly added our `worlds` folder in package environment. And also we defined executable script/node named `launch_maze_world` that will be present in `ros2_ws/src/maze_solver/maze_solver` directory. It is basically a python node that have a main function which will be called while executing that node with the alias `launch_maze_world`. This node will spawn the empty world with our maze model inside it.   
So we have to create that python node.


### 7. Create a python launch node to launch empty world with maze :
```
cd ~/ros2_ws/src/maze_solver/maze_solver
touch launch_maze_world.py
chmod +x launch_maze_world.py
```
Now open this `launch_maze_world.py` file & paste the below code into it
```
#!/usr/bin/env python3

import os
from ament_index_python.packages import get_package_share_directory
from launch import LaunchDescription
from launch.actions import ExecuteProcess
from launch_ros.actions import Node
from launch import LaunchService

def generate_launch_description():
    # Path to the maze_world.world file
    pkg_share = get_package_share_directory('maze_solver')
    world_file = os.path.join(pkg_share, 'worlds', 'empty_world.world')

    return LaunchDescription([
        # Launch Gazebo with the specified world file
        ExecuteProcess(
            cmd=['gazebo', '--verbose', world_file, '-s', 'libgazebo_ros_factory.so'],
            output='screen'
        ),
    ])

def main():
    ld = generate_launch_description()
    ls = LaunchService()
    ls.include_launch_description(ld)
    return ls.run()

if __name__ == '__main__':
    main()

```

### 8. Build and run the package node :
```
cd ~/ros2_ws
colcon build
source ~/.bashrc
ros2 run maze_solver launch_maze_world
```
It should open the gazebo empty world with a maze inside it. If you could follow up until this step, then go to the next step.   
Now the next task is to spawn the turtlebot3 model into this world. For that, we have to install `turtlebot3`, `turtlbot3_msgs`, `turtlebot3_simulations` packages with the `humble-devel` branch from ROBOTIS-GIT account. Try to do it yourself, use internet resources, understand the code.










