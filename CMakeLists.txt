cmake_minimum_required(VERSION 2.8.12)
project(pybind11_catkin)

find_package(catkin REQUIRED)

catkin_package(
  CFG_EXTRAS pybind11_catkin.cmake
)

install(DIRECTORY cmake
        DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
        PATTERN ".svn" EXCLUDE)
