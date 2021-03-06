cmake_minimum_required(VERSION 2.8.3)
project(prometheus_plan_manage)

set(CMAKE_BUILD_TYPE "Release")
set(CMAKE_CXX_FLAGS "-std=c++11")
set(CMAKE_CXX_FLAGS_RELEASE "-O3 -Wall -g")

find_package(Eigen3 REQUIRED)
find_package(PCL 1.7 REQUIRED)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  std_msgs
  geometry_msgs
  plan_env
  path_searching
  bspline_opt
  traj_utils
  message_generation
        sdf_tools
        sensor_msgs
)

# Generate messages in the 'msg' folder
add_message_files(
  FILES
  Bspline.msg
  )

# Generate added messages and services with any dependencies listed here
generate_messages(
  DEPENDENCIES
  std_msgs
  geometry_msgs
  sensor_msgs
)

catkin_package(
 INCLUDE_DIRS include
 LIBRARIES plan_manage
 CATKIN_DEPENDS plan_env path_searching bspline_opt traj_utils message_runtime pcl_conversions pcl_ros
#  DEPENDS system_lib
)

include_directories(${PROJECT_SOURCE_DIR}/../plan_env/ThirdParty/sdf_tools/include)
include_directories(${PROJECT_SOURCE_DIR}/../plan_env/ThirdParty/arc_utilities/include)

include_directories(
  include
  SYSTEM
  ${catkin_INCLUDE_DIRS} ${PROJECT_SOURCE_DIR}/include
  ${EIGEN3_INCLUDE_DIR}
  ${PCL_INCLUDE_DIRS}
  ${PROJECT_SOURCE_DIR}/../../../common/include
)


add_executable(dyn_planner_node
  src/dyn_planner_node.cpp 
  src/planning_fsm.cpp
  src/dyn_planner_manager.cpp
  src/tools
  )
target_link_libraries(dyn_planner_node 
  ${catkin_LIBRARIES}
  )

add_executable(traj_server 
src/traj_server.cpp)
target_link_libraries(traj_server ${catkin_LIBRARIES})
add_dependencies(traj_server ${${PROJECT_NAME}_EXPORTED_TARGETS})

add_executable(test_planning_static 
  src/test_planning_static.cpp)
target_link_libraries(test_planning_static 
  ${catkin_LIBRARIES}
  ${PCL_LIBRARIES}
  )

