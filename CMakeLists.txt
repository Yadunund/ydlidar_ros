cmake_minimum_required(VERSION 3.5.0)

project(ydlidar)

if (NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 14)
endif()

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
add_compile_options(-Wall -Wextra -Wpedantic)
endif()

find_package(ament_cmake REQUIRED)
find_package(rclcpp REQUIRED)
find_package(sensor_msgs REQUIRED)

#add_subdirectory(sdk)

set(SDK_PATH "./sdk/")

FILE(GLOB SDK_SRC 
  "${SDK_PATH}/src/impl/unix/*.cpp"
  "${SDK_PATH}/src/*.cpp"
  "${SDK_PATH}/src/*.c"
)

file(GLOB_RECURSE srcs "src/*.c")

#include_directories(
#  ${catkin_INCLUDE_DIRS}
#  ${PROJECT_SOURCE_DIR}/src
#  ${PROJECT_SOURCE_DIR}/sdk/include
#  ${PROJECT_SOURCE_DIR}/sdk/src
#)

add_executable(ydlidar_node src/ydlidar_node.cpp ${srcs})
# add_executable(ydlidar_node src/ydlidar_node.cpp  ${SDK_SRC})
add_executable(ydlidar_client src/ydlidar_client.cpp)

target_include_directories(ydlidar_node PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)

ament_target_dependencies(ydlidar_node
   rclcpp
   sensor_msgs
)

ament_target_dependencies(ydlidar_client
   rclcpp
   sensor_msgs
)

install(TARGETS ydlidar_node ydlidar_client
  RUNTIME DESTINATION lib/${PROJECT_NAME}
  LIBRARY DESTINATION lib/${PROJECT_NAME}
  ARCHIVE DESTINATION lib/${PROJECT_NAME}
)


install(DIRECTORY launch startup sdk
  DESTINATION share/${PROJECT_NAME}
)

ament_package()

