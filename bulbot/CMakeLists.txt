cmake_minimum_required(VERSION 3.5)
project(bulbot)

# Default to C99
if(NOT CMAKE_C_STANDARD)
  set(CMAKE_C_STANDARD 99)
endif()

# Default to C++14
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  add_compile_options(-Wall -Wextra -Wpedantic)
endif()

# find dependencies
find_package(ament_cmake REQUIRED)
# uncomment the following section in order to fill in
# further dependencies manually.
# find_package(<dependency> REQUIRED)

if(BUILD_TESTING)
  find_package(ament_lint_auto REQUIRED)
  # the following line skips the linter which checks for copyrights
  # uncomment the line when a copyright and license is not present in all source files
  #set(ament_cmake_copyright_FOUND TRUE)
  # the following line skips cpplint (only works in a git repo)
  # uncomment the line when this package is not in a git repo
  #set(ament_cmake_cpplint_FOUND TRUE)
  ament_lint_auto_find_test_dependencies()
endif()

install(
  DIRECTORY config description launch worlds
  DESTINATION share/${PROJECT_NAME}
)
find_package(catkin REQUIRED COMPONENTS
  gazebo_ros
  roscpp
  geometry_msgs
  std_msgs
  sensor_msgs
)
find_package(gazebo REQUIRED)

# Declare dependencies
catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES robot_tutorial_1
#  CATKIN_DEPENDS gazebo_ros roscpp
#  DEPENDS system_libi
  DEPENDS
    roscpp
    gazebo_ros
    message_runtime
)

# Link to gazebo
link_directories(
  ${GAZEBO_LIBRARY_DIRS}
)

# Include dirs.
include_directories(
# include
  ${catkin_INCLUDE_DIRS}
  ${roscpp_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
  ${GAZEBO_INCLUDE_DIRS}
)

# Compile plugin
add_library(wb_plugin plugins/wb_plugin.cc)
target_link_libraries(wb_plugin ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES} ${roscpp_LIBRARIES})

# Compile keyin
add_executable(keyin src/keyin.cc)
target_link_libraries(keyin ${catkin_LIBRARIES})
add_dependencies(keyin ${${PROJECT_NAME}_EXPORTED_TARGETS} ${catkin_EXPORTED_TARGETS})

# Lidar Plugin
add_library(lidar_plugin plugins/mylidar.cc)
target_link_libraries(lidar_plugin ${catkin_LIBRARIES} ${GAZEBO_LIBRARIES} ${roscpp_LIBRARIES} RayPlugin)

ament_package()
