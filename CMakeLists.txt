cmake_minimum_required(VERSION 2.8.3)
project(detectron2_ros)

find_package(catkin REQUIRED COMPONENTS
  rospy
  message_generation
  std_msgs
  sensor_msgs
)

## System dependencies are found with CMake's conventions
#find_package(catkin REQUIRED COMPONENTS std_msgs sensor_msgs message_generation)


## Uncomment this if the package has a setup.py. This macro ensures
## modules and global scripts declared therein get installed
## See http://ros.org/doc/api/catkin/html/user_guide/setup_dot_py.html
catkin_python_setup()

add_message_files(
  FILES
    Result.msg
)

generate_messages(
  DEPENDENCIES
  std_msgs
  sensor_msgs
)

catkin_package(
#  INCLUDE_DIRS include
#  LIBRARIES detectron2_ros
#  CATKIN_DEPENDS rospy
#  DEPENDS system_lib
)

###########
## Build ##
###########

## Specify additional locations of header files
## Your package locations should be listed before other locations
include_directories(
# include
  ${catkin_INCLUDE_DIRS}
)

#############
## Install ##
#############

## Mark executable scripts (Python etc.) for installation
## in contrast to setup.py, you can choose the destination
install(PROGRAMS
  src/detectron2_ros.py
  DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
)

## Mark cpp header files for installation
install(DIRECTORY
  msg
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

## Download model weights and trained weights
set(PATH "${CMAKE_CURRENT_SOURCE_DIR}/models")
set(MODELPATH "${PATH}/R-50.pkl")
if (NOT EXISTS "${MODELPATH}")
  message(STATUS " Unable to find R-50 pkl model weights. Downloading now ...")
  execute_process(COMMAND wget https://dl.fbaipublicfiles.com/detectron/ImageNetPretrained/MSRA/R-50.pkl -P ${PATH})
endif()
set(MODELPATH "${CMAKE_CURRENT_SOURCE_DIR}/models/R-50-crowdhuman.pth")
if (NOT EXISTS "${MODELPATH}")
  message(STATUS " Unable to find R-50 crowdhuman pth model weights. Downloading now ...")
  execute_process(COMMAND wget https://github.com/y-lai/Detectron2_ros/releases/download/v0.0/model_final.pth -P ${PATH})
endif()
set(MODELPATH "${CMAKE_CURRENT_SOURCE_DIR}/models/NoH-NMS-crowdhuman.pth")
if (NOT EXISTS "${MODELPATH}")
  message(STATUS " Unable to find NoH-NMS pth model weights. Downloading now ...")
  execute_process(COMMAND wget https://github.com/y-lai/Detectron2_ros/releases/download/v0.0/model_final_nohnms.pth -P ${PATH})
endif()
