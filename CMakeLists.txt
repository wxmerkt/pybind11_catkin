cmake_minimum_required(VERSION 2.8.12)
project(pybind11_catkin)

find_package(catkin REQUIRED)
include(ExternalProject)

ExternalProject_Add(pybind11_src
  URL "https://github.com/pybind/pybind11/archive/v2.2.4.zip"
  URL_MD5 "06f0bbf25dc9c4fe15092e9e03a8fc01"
  UPDATE_COMMAND ""
  CMAKE_ARGS -DPYBIND11_TEST:BOOL=OFF -DPYBIND11_INSTALL:BOOL=ON -DCMAKE_INSTALL_PREFIX:PATH=${PROJECT_BINARY_DIR}/install
)
ExternalProject_Add_Step(pybind11_src CopyToDevel
  COMMENT "Copying to devel"
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/share/cmake/pybind11/pybind11Tools.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_BINARY_DIR}/install/share/cmake/pybind11/FindPythonLibsNew.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/pybind11/ ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/pybind11/
  DEPENDEES install
)

if(IS_DIRECTORY ${CMAKE_INSTALL_PREFIX})
  message(WARNING "Compiling for install workspace (${CMAKE_INSTALL_PREFIX})")
  catkin_package(
    INCLUDE_DIRS include/${PROJECT_NAME}
    LIBRARIES ${PYTHON_LIBRARIES}
    CFG_EXTRAS pybind11_catkin.cmake
  )
else()
  message(WARNING "Compiling for devel workspace")
  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME})
  catkin_package(
    INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include ${EIGEN3_INCLUDE_DIR}
    LIBRARIES ${PYTHON_LIBRARIES}
    CFG_EXTRAS pybind11_catkin.cmake
  )
endif()

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(
  FILES ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake/FindPythonLibsNew.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake/FindPythonLibsNew.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake/pybind11Tools.cmake
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
