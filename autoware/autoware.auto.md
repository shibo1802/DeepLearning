# Installation

## With ADE

https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/installation-ade.html

## Without ADE

https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/installation-no-ade.html

[Installing ROS 2 on Ubuntu Linux](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Binary.html)
[Installing ROS 2 via Debian Packages - Ubuntu focal](https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html)
[Build Autoware.auto](https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/building.html)

docker image: docker pull boyue1802/deep_learning:nvidia-cuda-11.6.0-devel-ubuntu20.04-ROS2

## Tier4
https://tier4.github.io/autoware.proj/tree/main/tutorial/HowToInstall/

# Design
https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/design.html

## Common
## Neural networks design
https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/neural-networks-design.html

## Perception
### Segmentation
#### apollo_lidar_segmentation

https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/apollo-lidar-segmentation-design.html

https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/apollo-lidar-segmentation-nodes-design.html

[How to train the model - baiducnn](https://github.com/kosuke55/train_baiducnn)

# Build
```
export ROS_VERSION=2
export ROS_DISTRO=foxy
export COLCON_DEFAULTS_FILE=$(realpath tools/ade_image/colcon-defaults.yaml)
source /opt/ros/$ROS_DISTRO/setup.bash

vcs import < autoware.auto.$ROS_DISTRO.repos
git lfs pull --exclude="" --include="*"

colcon build # --parallel-workers 1 --cmake-args -DCMAKE_BUILD_TYPE=RelWithDebInfo

# colcon test
# colcon test-result --verbose
```

# Usage
https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/usage.html

## 3D perception stack
### Run simulator
 ```
ade --rc .aderc-lgsvl start --update --enter

# run lgsvl

ade$ ros2 run robot_state_publisher robot_state_publisher /opt/AutowareAuto/share/lexus_rx_450h_description/urdf/lexus_rx_450h.urdf
```

### Bridge
```
sudo apt update
sudo apt install ros-foxy-lgsvl-bridge
lgsvl_bridge
```

### Launch Visualization
```
$ ade enter
ade$ source /opt/AutowareAuto/setup.bash
ade$ rviz2 -d /opt/AutowareAuto/share/autoware_auto_examples/rviz2/autoware_perception_stack.rviz
```

### Launch Perception Nodes
https://autowarefoundation.gitlab.io/autoware.auto/AutowareAuto/perception-stack-howto.html

```
$ ade enter
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run point_cloud_filter_transform_nodes point_cloud_filter_transform_node_exe --ros-args --remap __ns:=/lidar_front --params-file /opt/AutowareAuto/share/point_cloud_filter_transform_nodes/param/vlp16_sim_lexus_filter_transform.param.yaml --remap __node:=filter_transform_vlp16_front  --remap points_in:=/lidar_front/points_xyzi
```

```
$ ade enter
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run ray_ground_classifier_nodes ray_ground_classifier_cloud_node_exe --ros-args --params-file /opt/AutowareAuto/share/ray_ground_classifier_nodes/param/vlp16_lexus_pcap.param.yaml --remap points_in:=/lidar_front/points_filtered
```

```
$ ade enter
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run euclidean_cluster_nodes euclidean_cluster_node_exe --ros-args --params-file /opt/AutowareAuto/share/euclidean_cluster_nodes/param/vlp16_lexus_cluster.param.yaml --remap points_in:=/points_nonground
```

```
$ ade enter
ade$ source /opt/AutowareAuto/setup.bash
ade$ ros2 run euclidean_cluster_nodes euclidean_cluster_node_exe --ros-args --params-file /opt/AutowareAuto/share/euclidean_cluster_nodes/param/vlp16_lexus_cluster_as_polygon.param.yaml --remap points_in:=/points_nonground
```

#### Aopllo CNN
- Run necessary nodes, e.g. sensors, rviz2
```
$ ade enter
ade$ source install/setup.sh
ade$ ros2 launch install/autoware_demos/share/autoware_demos/launch/sim_lidar_camera_projection.launch.py

# Comment the euclidean_clustering since it has the same outputs with apollo_lidar_segmentation
```

- bridge
```
lgsvl-bridge # The lgsvl simulation must be ROS2
```

- Run Apollo CNN node
```
ros2 launch install/apollo_lidar_segmentation_nodes/share/apollo_lidar_segmentation_nodes/launch/lidar_segmentation.launch.py
```

- preprocess
```
autoware::perception::segmentation::apollo_lidar_segmentation::ApolloLidarSegmentationPreProcessor::schedule
```

- executation
```
tvm_utility::pipeline::InferenceEngineTVM::schedule
```

- postprocess
```
autoware::perception::segmentation::apollo_lidar_segmentation::ApolloLidarSegmentationPostProcessor::schedule
```

- Obstacle To Object
```
autoware::perception::segmentation::apollo_lidar_segmentation::Cluster2D::obstacleToObject
```

- Output topics
```
lidar_bounding_boxes and lidar_bounding_boxes_viz
```
