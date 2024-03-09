# slam-2d-lidar-demo
A SLAM demo using the RTABmap package and RPLidar

## Cloning this project

`git clone --recurse-submodules https://github.com/cornellev/slam-2d-lidar-demo.git`

## Running RPLidar

Give permissions to the RPLidar
```
ls -l /dev |grep ttyUSB
sudo chmod 666 /dev/ttyUSB0
```

Run the RPLidar package
```
roslaunch rplidar_ros rplidar_a1.launch
```

This works to upload to the LiDar data to the `/scan` topic (topic name can be configured). Also recommended to setup a laser frame in the transform tree. More can be found in the [rplidar ros documentation](https://github.com/robopeak/rplidar_ros).

## Running RTABMap

rtabmap is not included as a submodule, per the [rtabmap instruction guidelines](https://github.com/robopeak/rplidar_ros), which suggests installing it globally.



