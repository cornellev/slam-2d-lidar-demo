# slam-2d-lidar-demo
A SLAM demo using the RTABmap package and RPLidar

## Cloning this project

`git clone --recurse-submodules https://github.com/cornellev/slam-2d-lidar-demo.git`

(Make sure to clone submodules)

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

To start simultaneous localization and mapping from the previously saved map (`~/.ros/rtabmap.db`), call:
```
roslaunch lidar_2d_demo slam.launch
```

To start RTABmap on a new map,
```
roslaunch lidar_2d_demo slam.launch rtabmap_args:=--delete_db_on_start
```

To run in localization mode
```
roslaunch lidar_2d_demo slam.launch localization:=true
```

## Breakdown

The RPLidar package publishes just the /scan message with the frame_id "laser" (this can be configured through launch files).

The RTABmap package expects some odometry and some scan of the environment. Because we are only working with a lidar we need to calculate odometry with the scan data and feed the scan data into RTABmap.

To handle odometry, RTABmap provides the rtabmap_odom package and the icp_odometry node (alternatives are stereo_ and rgbd_ odometry) for calculating odometry from scans. This package requires the base_link -> laser frame (or whatever id the scan data is published under) to be provided. icp_odometry calculates the odometry and publishes it under /odom topic and provides the odom -> base_link frame.

The /odom data and the /scan data are fed into the rtabmap_slam package. The node is configured to use lidar scan data (e.g. subscribe only to /scan topic). Finally, the node will publish the occupancy grid under /grid_map and publishes the map -> odom frame. 

[rtabmap_ros documentation](http://wiki.ros.org/rtabmap_slam)

## Bag Files

[Mapping ELL workspace / hallway](https://drive.google.com/file/d/1LOp4zhcIXm-IjB1JhrdJo5Z-l5ldpCAE/view?usp=sharing)

## Notes

There are a lot of parameters in RTABmap and learning more of them will help tuning, debugging, and improving performance.

Another common issue is that when the odometry node cannot caluclate the transformation, it sends a null transform. This breaks everything that relies on the transform (the slam package). I can experiment with the `publish_null_when_lost` parameter of rtabmap_odom.

There may be better packages for doing LiDAR only mapping (e.g. gmapping).

I'd like to be able to recreate results in simulation (e.g. go from simulated lidar data to map), but have been unable to because of issues with timing and transforms (e.g. a transform doesn't exist a certain time, this may be able to be fixed with using tf2).