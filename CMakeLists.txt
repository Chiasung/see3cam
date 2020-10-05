cmake_minimum_required(VERSION 2.8.3)
project(tara_camera)

find_package(catkin REQUIRED COMPONENTS camera_info_manager image_transport nodelet roscpp sensor_msgs)

find_package(Boost REQUIRED COMPONENTS thread system)

FIND_PACKAGE(PkgConfig)
PKG_CHECK_MODULES(PC_GLIB QUIET glib-2.0)

FIND_LIBRARY(
  GLIB_LIBRARIES
  NAMES glib-2.0
  HINTS ${PC_GLIB_LIBDIR}
  ${PC_GLIB_LIBRARY_DIRS}
  )

# Files in glib's main include path may include glibconfig.h, which,
# for some odd reason, is normally in $LIBDIR/glib-2.0/include.
GET_FILENAME_COMPONENT(_GLIB_LIBRARY_DIR ${GLIB_LIBRARIES} PATH)
FIND_PATH(
  GLIBCONFIG_INCLUDE_DIR
  NAMES glibconfig.h
  HINTS ${PC_LIBDIR} ${PC_LIBRARY_DIRS} ${_GLIB_LIBRARY_DIR}
  PATH_SUFFIXES glib-2.0/include
  )

FIND_PATH(
  GLIB_INCLUDE_DIR
  NAMES glib.h
  HINTS ${PC_GLIB_INCLUDEDIR}
  ${PC_GLIB_INCLUDE_DIRS}
  PATH_SUFFIXES glib-2.0
  )

SET(GLIB_INCLUDE_DIRS ${GLIB_INCLUDE_DIR} ${GLIBCONFIG_INCLUDE_DIR})

catkin_package(
  INCLUDE_DIRS include
  #	LIBRARIES nodelet_uvc_camera
  CATKIN_DEPENDS roscpp nodelet sensor_msgs
  #  DEPENDS system_lib
  )

include_directories(
  include
  ${catkin_INCLUDE_DIRS}
  ${CMAKE_SOURCE_DIR}/../src/see3cam/src/xunit
  ${GLIB_INCLUDE_DIRS}
  )

# add_library(xunit
#   src/xunit/xunit_lib.c
#   src/xunit/xunit_lib_cug.c
# )

# add_library(xunitTara
#   src/xunit/xunit_lib_tara.h
#   src/xunit/xunit_lib_tara.cpp
#   ) 

# target_link_libraries(xunitTara
#   ${udev_LIBRARIES}
#   ${GLIB2_LIBRARIES})  

# target_link_libraries(xunit
#   ${udev_LIBRARIES}
#   )

# add_library(nodelet_uvc_camera
#   src/nodelets.cpp
#   src/camera.cpp
#   src/stereo.cpp
#   src/uvc_cam.cpp)

# target_link_libraries(nodelet_uvc_camera ${Boost_LIBRARIES} ${catkin_LIBRARIES} xunit)

# add_executable(uvc_camera_node
#   src/camera_node.cpp
#   src/camera.cpp
#   src/uvc_cam.cpp)

# target_link_libraries(uvc_camera_node
#   ${Boost_LIBRARIES}
#   ${catkin_LIBRARIES}
#   xunit
#   xunitTara -lv4l2 -ludev -lglib-2.0)

add_executable(tara_node
  src/tara_node.cpp
  src/tara_ros.cpp
  src/uvc_cam.cpp
  src/xunit_lib_tara.cpp
  )

target_link_libraries(tara_node
  ${Boost_LIBRARIES}
  ${catkin_LIBRARIES}
  ${GLIB2_LIBRARIES} 
  -lv4l2 -lyaml-cpp -ludev -lglib-2.0)

#############
## Install ##
#############

# ## Mark executables and/or libraries for installation
# install(TARGETS uvc_camera_node
# 	ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
# 	LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
# 	RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
# )

install(TARGETS tara_node
  ARCHIVE DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  LIBRARY DESTINATION ${CATKIN_PACKAGE_LIB_DESTINATION}
  RUNTIME DESTINATION ${CATKIN_PACKAGE_BIN_DESTINATION}
  )

# install(FILES
#   nodelet_uvc_camera.xml
#   DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
# )

