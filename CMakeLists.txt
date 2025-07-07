cmake_minimum_required(VERSION 3.3)
cmake_policy(SET CMP0048 NEW)
project(brace_top_setup VERSION 1.0.6)

if(NOT DISABLE_ROS)
  if(NOT DEFINED ENV{ROS_VERSION})
    message(FATAL_ERROR "ROS_VERSION is not set in the environment")
  endif()
  if($ENV{ROS_VERSION} EQUAL 1)
    set(ROS_VERSION 1)
    find_package(catkin REQUIRED)
  elseif($ENV{ROS_VERSION} EQUAL 2)
    set(ROS_VERSION 2)
    find_package(ament_cmake REQUIRED)
  else()
    message(FATAL_ERROR "Unknown ROS version: $ENV{ROS_VERSION}")
  endif()
else()
  set(ROS_VERSION 0)
endif()

if(${ROS_VERSION} EQUAL 2)
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/${PROJECT_NAME})
elseif(${ROS_VERSION} EQUAL 1)
  catkin_package()
  set(CATKIN_PACKAGE_SHARE_DESTINATION share/mc_rtc/${PROJECT_NAME})

  include(CMakePackageConfigHelpers)
  set(VERSION_CONFIG
      "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
  set(PROJECT_CONFIG "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")
  set(CONFIG_INSTALL_DIR "lib/cmake/${PROJECT_NAME}")

  configure_file("${CMAKE_CURRENT_SOURCE_DIR}/cmake/ConfigVersion.cmake.in"
                 "${VERSION_CONFIG}" @ONLY)
  configure_package_config_file(
    "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Config.cmake.in" "${PROJECT_CONFIG}"
    INSTALL_DESTINATION "${CONFIG_INSTALL_DIR}")

  install(FILES "${VERSION_CONFIG}" "${PROJECT_CONFIG}"
          DESTINATION "${CONFIG_INSTALL_DIR}")
  install(FILES package.xml DESTINATION "${CATKIN_PACKAGE_SHARE_DESTINATION}")
endif()

# install all .txt files in convex folder
set(dir convex)
install(
  DIRECTORY ${dir}/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir}
  FILES_MATCHING
  PATTERN "*.txt")

# install all .urdf files in urdf folder

set(dir urdf)
install(
  DIRECTORY ${dir}/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir}
  FILES_MATCHING
  PATTERN "*.urdf")

# install all .stl or .STL files in meshes folder
set(dir meshes)
install(
  DIRECTORY ${dir}/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/${dir}
  FILES_MATCHING
  PATTERN "*.stl"
  PATTERN "*.STL")

if(${ROS_VERSION} EQUAL 2)
  ament_package(CONFIG_EXTRAS cmake/${PROJECT_NAME}_INSTALL_PREFIX.cmake)
endif()
