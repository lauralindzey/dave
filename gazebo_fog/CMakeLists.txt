cmake_minimum_required(VERSION 2.8.3)
project(gazebo_fog)

find_package(catkin REQUIRED tf gazebo_plugins)

find_package(gazebo REQUIRED)
find_package(roscpp REQUIRED)
find_package(std_msgs REQUIRED)
find_package(OpenCV)
include_directories(${roscpp_INCLUDE_DIRS})
include_directories(${std_msgs_INCLUDE_DIRS})
include_directories(
  src_ros src_gazebo
  ${catkin_INCLUDE_DIRS} 
  ${GAZEBO_INCLUDE_DIRS}
)
link_directories(${GAZEBO_LIBRARY_DIRS})
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${GAZEBO_CXX_FLAGS}")

catkin_package()

# Gazebo fog camera World plugin
add_library(FogCameraPlugin SHARED src_gazebo/FogCameraPlugin.cc
                                   src_gazebo/FogCameraPlugin.hh)
target_link_libraries(FogCameraPlugin ${GAZEBO_LIBRARIES})

# Gazebo fog camera sensor loaded by Gazebo fog camera World plugin
add_library(FogCameraSensor SHARED
            src_gazebo/add_fog_camera_sensor.cc
            src_gazebo/FogCameraSensor.cc
            src_gazebo/FogCameraSensor.hh
            src_gazebo/FogCameraSensorPrivate.hh)
target_link_libraries(FogCameraSensor ${GAZEBO_LIBRARIES})

# ROS fog camera sensor
add_library(gazebo_ros_fog_camera_plugin
            src_gazebo/FogCameraSensor.hh
            src_ros/gazebo_ros_fog_camera.h
            src_ros/gazebo_ros_fog_camera.cpp)
target_link_libraries(FogCameraPlugin ${catkin_LIBRARIES})

# for launch
install(DIRECTORY launch
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
        PATTERN "*~" EXCLUDE)

