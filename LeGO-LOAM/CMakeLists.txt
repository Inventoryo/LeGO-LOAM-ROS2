cmake_minimum_required(VERSION 3.10.0)
project(lego_loam)

find_package(tf2  REQUIRED)
find_package(tf2_ros  REQUIRED)
find_package(rclcpp   REQUIRED)
find_package(cv_bridge  REQUIRED)
find_package(image_transport  REQUIRED)
find_package(pcl_ros  REQUIRED)
find_package(std_msgs  REQUIRED)
find_package(sensor_msgs  REQUIRED)
find_package(geometry_msgs  REQUIRED)
find_package(nav_msgs  REQUIRED)
find_package(cloud_msgs  REQUIRED)

set(dependencies
  tf2
  tf2_ros
  rclcpp
  cv_bridge
  image_transport
  pcl_ros
  std_msgs
  sensor_msgs
  geometry_msgs
  nav_msgs
  cloud_msgs
)

find_package(GTSAM REQUIRED QUIET)
find_package(PCL REQUIRED QUIET)
find_package(OpenCV REQUIRED QUIET)
find_package(VTK REQUIRED QUIET)

include_directories(
	include
	${PCL_INCLUDE_DIRS}
	${OpenCV_INCLUDE_DIRS}
	${GTSAM_INCLUDE_DIR}
)

link_directories(
	include
	${OpenCV_LIBRARY_DIRS}
	${PCL_LIBRARY_DIRS}
	${GTSAM_LIBRARY_DIRS}
)

add_executable(imageProjection src/imageProjection.cpp)
ament_target_dependencies(imageProjection ${dependencies})
target_link_libraries(imageProjection ${PCL_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(featureAssociation src/featureAssociation.cpp)
ament_target_dependencies(featureAssociation ${dependencies} )
target_link_libraries(featureAssociation  ${PCL_LIBRARIES} ${OpenCV_LIBRARIES})

add_executable(mapOptmization src/mapOptmization.cpp)
ament_target_dependencies(mapOptmization ${dependencies})
target_link_libraries(mapOptmization ${PCL_LIBRARIES} ${OpenCV_LIBRARIES} gtsam)

add_executable(transformFusion src/transformFusion.cpp)
ament_target_dependencies(transformFusion ${dependencies})
target_link_libraries(transformFusion  ${PCL_LIBRARIES} ${OpenCV_LIBRARIES})

install(TARGETS
  imageProjection
  featureAssociation
  mapOptmization
  transformFusion
  DESTINATION lib/${PROJECT_NAME})

ament_export_dependencies(rosidl_default_runtime)

ament_package()