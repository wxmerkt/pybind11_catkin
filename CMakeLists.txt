cmake_minimum_required(VERSION 3.4)
project(pybind11_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

ExternalProject_Add(pybind11_src
  URL "https://github.com/pybind/pybind11/archive/v2.6.0.zip"
  UPDATE_COMMAND ""
  CMAKE_ARGS -DPYBIND11_NOPYTHON=TRUE
             -DPYBIND11_TEST=OFF
             -DPYBIND11_INSTALL=ON
             -DCMAKE_INSTALL_PREFIX=${PROJECT_BINARY_DIR}/install
             -DCMAKE_INSTALL_INCLUDEDIR=${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}
             -DCMAKE_INSTALL_DATAROOTDIR=${CATKIN_DEVEL_PREFIX}/share
)

catkin_package(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/include
  CFG_EXTRAS pybind11_catkin.cmake
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/share
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)

