cmake_minimum_required(VERSION 2.8.3)
project(yocs_navigator)

find_package(catkin REQUIRED COMPONENTS
  roscpp
  actionlib
  tf
  yocs_math_toolkit
  yocs_msgs
  move_base_msgs
  nav_msgs
)

## System dependencies are found with CMake's conventions
find_package(Boost REQUIRED COMPONENTS system)

catkin_package(
  INCLUDE_DIRS include
  LIBRARIES yocs_navigator
  CATKIN_DEPENDS roscpp actionlib tf yocs_math_toolkit move_base_msgs nav_msgs yocs_msgs 
  DEPENDS Boost
)

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${Boost_INCLUDE_DIRS}
)

# Declare a cpp library
add_library(yocs_basic_move_controller
  src/${PROJECT_NAME}/basic_move_controller.cpp
)

target_link_libraries(yocs_basic_move_controller ${catkin_LIBRARIES})
add_dependencies(yocs_basic_move_controller yocs_msgs_gencpp geometry_msgs_gencpp nav_msgs_gencpp)

add_library(yocs_semantic_navigator
  src/${PROJECT_NAME}/semantic_navigator.cpp
  src/${PROJECT_NAME}/navigation_handler.cpp
  src/${PROJECT_NAME}/utils.cpp
)

target_link_libraries(yocs_semantic_navigator ${catkin_LIBRARIES} yocs_basic_move_controller)
add_dependencies(yocs_semantic_navigator yocs_msgs_gencpp move_base_msgs_gencpp yocs_basic_move_controller)


## Declare a cpp executable
add_executable(yocs_semantic_navigator_node 
  src/semantic_navigator_main.cpp
)

## Add cmake target dependencies of the executable/library
## as an example, message headers may need to be generated before nodes
add_dependencies(yocs_semantic_navigator_node yocs_semantic_navigator)

## Specify libraries to link a library or executable target against
target_link_libraries(yocs_semantic_navigator_node yocs_semantic_navigator)

#############
## Install ##
#############

install(TARGETS yocs_basic_move_controller
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

# Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
  PATTERN ".svn" EXCLUDE
)
