# ROS Openpose Setup Guide

## Tested Environment
1. Ubuntu 20.04.2 LTS
2. Intel RealSense Camera
3. Python 3.8.10
4. Ros Noetic

## Step by step installation (CPU Mode Only)
1. Follow the tutorial [here](https://github.com/CMU-Perceptual-Computing-Lab/openpose/blob/master/doc/installation/0_index.md#compiling-and-running-openpose-from-source) to build OpenPose from the source. Please note the following points when configuring CMAKE with GUI:
    * Enable the CPU_ONLY flag
    * Enable the BUILD_PYTHON flag
    
    And after configuring CMake successfully, don't forget to compile it.

2. Install [OpenPose](https://github.com/CMU-Perceptual-Computing-Lab/openpose)

    Although it's recommended to install OpenPose in the project-wide, you can also install it system-widely if you are the only user of the machine.
    
    * System-wide Installation (Default and easier)
    ```
    cd openpose/build
    sudo make install
    ```

    * Project-wide Installation (Recommended but require extra steps)
    ```
    cd openpose/build
    mkdir dependencies
    make DESTDIR=./dependencies install
    export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:<FULL_PATH_TO_DEPENDENCY_FOLDER>/usr/local/lib
    sudo ldconfig
    ```
    


2. Install [realsense-ros](https://github.com/IntelRealSense/realsense-ros)
    ```
    sudo apt-get install ros-$ROS_DISTRO-realsense2-camera
    sudo apt-get install ros-$ROS_DISTRO-realsense2-description
    ```

3. After the successful installation of above dependencies, we can install ROS OpenPose itself. Follow the [installation section](https://github.com/ravijo/ros_openpose#installation) of the offical documentation. Note that we need to choose the latest version. Please check out the following version after cloning the repository:
    ```
    git checkout tags/v1.7.0
    ```

    Note that if you chose project-wide installation in Step 2, the following adaptations of the root CMakeLists file of ROS Openpose is necessary before you run catkin_make.
    ```
    ...
    set(OpenPose_DIR <FULL_PATH_TO_DEPENDENCY_FOLDER>/usr/local/lib/OpenPose)
    set(CUSTOMIZED_LIBRARY_DIRS <FULL_PATH_TO_DEPENDENCY_FOLDER>/usr/local/lib)
    ...

    link_directories(
    ${catkin_LIBRARY_DIRS}
    ${CUSTOMIZED_LIBRARY_DIRS}
    ...
    )

    ```
    An example of the root CMakeLists is [here](./CMakeLists.txt).
4. Configure your ROS OpenPose by following this [section](https://github.com/ravijo/ros_openpose/blob/47aca00ea38c3e3961483b9cb89fd99143ec0b13/README.md#configuration).

## Test your installation
```
# Terminal 1
roslaunch ros_openpose run.launch
# Terminal 2
rostopic echo /frame
```

You can see some message of the topic as follows:
```
header: 
  seq: 0
  stamp: 
    secs: 1632753377
    nsecs: 638708418
  frame_id: "camera_color_optical_frame"
persons: 
  - 
    bodyParts: 
      - 
        score: 0.8097620010375977
        pixel: 
          x: 509.5007019042969
          y: 208.00967407226562
        point: 
          x: 1.7334184646606445
          y: -0.35353878140449524
          z: 5.62000036239624
      ...
      ...
      ...
      - 
        score: 0.0
        pixel: 
          x: 0.0
          y: 0.0
        point: 
          x: -1.644964337348938
          y: -1.2620556354522705
          z: 3.122000217437744
    leftHandParts: []
    rightHandParts: []
---
```


## Troubleshooting
1. Cannot find "/usr/bin/env python", try this
    ```
    #!/usr/bin/env python3
    ```
2. Can Not find Protobuf, try this
    ```
    sudo apt-get install protobuf-compiler libprotobuf-dev
    ```
3. Can Not find OpenCV
    ```
    sudo apt update
    sudo apt install libopencv-dev python3-opencv
    ```
4. Unsupported GNU version! gcc versions later than 8 are not supported, try configure [gcc alternative](https://en.opensuse.org/User:Tsu2/gcc_update-alternatives) 

5. Can Not fin certain lib folder, try this
    ```
    sudo apt-get install libatlas-base-dev
    sudo apt-get install libhdf5-dev
    sudo apt install libboost-filesystem1.71-dev

    ```

## TODO
1. GPU Mode

## Contact
Feel free to contact me if anything is unclear to you :)
