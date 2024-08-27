cmake_minimum_required(VERSION 3.8)
project(aloam_velodyne)

# set(CMAKE_BUILD_TYPE "Release")
# # set(CMAKE_CXX_FLAGS "-std=c++11")
# set(CMAKE_CXX_STANDARD 17)
# set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

# ADD_COMPILE_OPTIONS(-std=c++14)
# ADD_COMPILE_OPTIONS(-std=c++14)
# set(CMAKE_CXX_FLAGS "-std=c++14 -O3")
# set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -fexceptions")
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
# find_package(catkin REQUIRED COMPONENTS
#   geometry_msgs
#   nav_msgs
#   sensor_msgs
#   roscpp
#   rospy
#   rosbag
#   std_msgs
#   image_transport
#   cv_bridge
#   tf
# )
# ROS dependencies
find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(rclcpp_components REQUIRED)
find_package(geometry_msgs REQUIRED)
find_package(nav_msgs REQUIRED)
find_package(sensor_msgs REQUIRED)
find_package(std_msgs REQUIRED)
find_package(rosbag2 REQUIRED)
find_package(std_srvs REQUIRED)
find_package(image_transport REQUIRED)
find_package(cv_bridge REQUIRED)
find_package(tf2 REQUIRED)
find_package(pcl_ros REQUIRED)
find_package(pcl_conversions REQUIRED)
# find_package(livox_ros_driver2 REQUIRED)
find_package(Boost REQUIRED COMPONENTS date_time)

find_package(Eigen3 REQUIRED)
find_package(PCL REQUIRED)
find_package(OpenCV REQUIRED)

find_package(Ceres REQUIRED)

find_package(OpenMP REQUIRED)
find_package(GTSAM REQUIRED QUIET)

set(dependencies
  rclcpp
  rclcpp_components
  geometry_msgs
  nav_msgs
  sensor_msgs
  std_msgs
  std_srvs
  image_transport
  cv_bridge
  pcl_ros
  pcl_conversions
)

# include_directories(
#   include
# 	${catkin_INCLUDE_DIRS} 
# 	${PCL_INCLUDE_DIRS}
#   ${CERES_INCLUDE_DIRS}
#   ${OpenCV_INCLUDE_DIRS}
#   ${GTSAM_INCLUDE_DIR}
# )

# catkin_package(
#   CATKIN_DEPENDS geometry_msgs nav_msgs roscpp rospy std_msgs
#   DEPENDS EIGEN3 PCL 
#   INCLUDE_DIRS include
# )

ament_export_dependencies(rosidl_default_runtime)

add_executable(ascanRegistration src/scanRegistration.cpp)
target_include_directories(ascanRegistration PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${PCL_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)
# target_link_libraries(ascanRegistration ${catkin_LIBRARIES} ${PCL_LIBRARIES})
target_link_libraries(ascanRegistration ${PCL_LIBRARIES} ${PYTHON_LIBRARIES})
ament_target_dependencies(ascanRegistration ${dependencies})


add_executable(alaserOdometry src/laserOdometry.cpp)
# target_link_libraries(alaserOdometry ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${CERES_LIBRARIES})
target_include_directories(alaserOdometry PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${PCL_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)
target_link_libraries(alaserOdometry ${PCL_LIBRARIES} ${CERES_LIBRARIES})
ament_target_dependencies(alaserOdometry ${dependencies})

add_executable(alaserMapping src/laserMapping.cpp)
target_include_directories(alaserMapping PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${PCL_INCLUDE_DIRS}
)
# target_link_libraries(alaserMapping ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${CERES_LIBRARIES})
target_link_libraries(alaserMapping ${PCL_LIBRARIES} ${CERES_LIBRARIES})
ament_target_dependencies(alaserMapping ${dependencies})

add_executable(alaserPGO 
  src/laserPosegraphOptimization.cpp
  include/scancontext/Scancontext.cpp
)
ament_target_dependencies(alaserPGO ${dependencies})
target_include_directories(alaserPGO PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
  ${PCL_INCLUDE_DIRS}
  ${OpenCV_INCLUDE_DIRS}
)
# target_include_directories(alaserPGO PUBLIC
#   $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#   $<INSTALL_INTERFACE:include>
#   ${PCL_INCLUDE_DIRS}
#   ${OpenCV_INCLUDE_DIRS}
# )
target_compile_options(alaserPGO 
  PRIVATE ${OpenMP_CXX_FLAGS}
)
target_link_libraries(alaserPGO 
  ${PCL_LIBRARIES} ${CERES_LIBRARIES}
  ${OpenMP_CXX_FLAGS}
  gtsam
)
#




# target_link_libraries(alaserPGO 
#   ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${CERES_LIBRARIES}
#   ${OpenMP_CXX_FLAGS}
#   gtsam
# )

# add_executable(kittiHelper src/kittiHelper.cpp)
# # target_link_libraries(kittiHelper ${catkin_LIBRARIES} ${PCL_LIBRARIES} ${OpenCV_LIBS})
# target_include_directories(kittiHelper PUBLIC
#   $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
#   $<INSTALL_INTERFACE:include>
#   ${PCL_INCLUDE_DIRS}
#   ${OpenCV_INCLUDE_DIRS}
# )
# ament_target_dependencies(kittiHelper ${dependencies})
# target_link_libraries(kittiHelper ${PCL_LIBRARIES} ${OpenCV_LIBS})




