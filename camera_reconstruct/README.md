# Camera Reconstruct Package
### Author: Blocked

#### This package contains a pipeline to save multiple point clouds and fuse them into a single point cloud. The package provides a service to save a point clouds and sends saved point clouds through pipeline. This package is ideal for 3D scanning.

![](videos/octopus_cropped.gif)

#### Usage Instructions:
1. Add package to the src folder in your ROS workspace
1. Compile: `catkin_make`
1. Connect Real Sense Camera
1. Start simulation: `roslaunch camera_reconstruct viewer.launch gaz:=true`
1. Use /generate_cp/save_pc to save current point cloud, transform point cloud about TurtleBot base_scan frame, and crop point cloud
    * If the service is called multiple times, all point clouds will be fused and published to /fused_pc topic

#### Configuration Instructions:
1. viewer.launch configuration
    * By default launches Realsense, rviz, generate_pc, and fuse_pc
    * The viewer launch file contains arguements gaz and clip
    * Set clip true to use a camera clipping distance of 0.7
    * Set gaz to true to launch a point cloud viewer without a physical TurtleBot or Sawyer
        * The TurtleBot is launch in Gazebo and the Sawyer is not launched
        * Publish twist messages to /cmd_vel while calling /generate_pc/save_pc to test point_cloud fusion pipeline
    * Modify PointCloud2 topic in rviz to see live point cloud, last saved point cloud, or fused point cloud

#### Saving Pointclouds:
1. Use `rosrun pcl_ros pointcloud_to_pcd input:=/fused_pc` to save a .pcd file of the fused pointcloud to the current directory
1. .pcd files can be viewed in pcl_viewer by using the command `pcl_viewer -multiview 1 path_to_.pcd `

#### Nodes:
1. generate_pc
    * Subscribes to PointCloud2 messages from Realsense camera
    * Provides a service to transform and publish current pointcloud to /saved_pcs topic
1. generate_pc_sim
    * Same functionality as generate_pc but works in simulation without the Sawyer robot
    * Used for at home testing
1. fuse_pc
    * Subscribes to /saved_pcs topic
    * Concatenates all saved_pcs into a single PointCloud2 message
    * Crops fused pointcloud
    * Publishes resulting pointcloud to /fused_pc
1. test_pointcloud
    * Test file to ensure save_pc service is functional