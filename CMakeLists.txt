cmake_minimum_required(VERSION 3.0.2)
project(pybind11_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

ExternalProject_Add(pybind11_src
  URL "https://github.com/pybind/pybind11/archive/v2.5.0.zip"
  UPDATE_COMMAND ""
  CMAKE_ARGS -DPYBIND11_PYTHON_VERSION=${PYTHON_VERSION_STRING}
             -DPYBIND11_TEST:BOOL=OFF -DPYBIND11_INSTALL:BOOL=ON
             -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install
)
ExternalProject_Add_Step(pybind11_src CopyToDevel
  COMMENT "Copying to devel"
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/share/cmake/pybind11/pybind11Tools.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/share/cmake/pybind11/FindPythonLibsNew.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/pybind11/ ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/pybind11/
  DEPENDEES install
)

file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME})
catkin_package(
  INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}
  CFG_EXTRAS pybind11_catkin.cmake
)

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(
  FILES
    ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake/FindPythonLibsNew.cmake
    ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake/pybind11Tools.cmake
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}/cmake
)
