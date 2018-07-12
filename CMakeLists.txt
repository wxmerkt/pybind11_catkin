cmake_minimum_required(VERSION 2.8.12)
project(pybind11_catkin)

find_package(catkin REQUIRED)

include(ExternalProject)

ExternalProject_Add(pybind11
  URL "https://github.com/pybind/pybind11/archive/v2.2.3.zip"
  URL_MD5 "6d9ae6508d0bb0595129dca09ec7738b"

  UPDATE_COMMAND ""
  PATCH_COMMAND ""
  TEST_COMMAND ""

  CMAKE_ARGS -DPYBIND11_TEST=No -DPYBIND11_INSTALL=Yes -DCMAKE_INSTALL_PREFIX=${CATKIN_DEVEL_PREFIX}
)
ExternalProject_Add_Step(pybind11
  MOVEToShared
  COMMENT "Copying CMake extras to install directory."
  COMMAND ${CMAKE_COMMAND} -E copy ${CATKIN_DEVEL_PREFIX}/share/cmake/pybind11/pybind11Tools.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E copy ${CATKIN_DEVEL_PREFIX}/share/cmake/pybind11/FindPythonLibsNew.cmake ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  COMMAND ${CMAKE_COMMAND} -E remove_directory ${CATKIN_DEVEL_PREFIX}/share/cmake/pybind11/
  COMMAND ${CMAKE_COMMAND} -E copy_directory ${CATKIN_DEVEL_PREFIX}/include/pybind11/ ${CATKIN_DEVEL_PREFIX}/include/pybind11_catkin/pybind11/
  DEPENDEES install
)

if(IS_DIRECTORY ${CMAKE_INSTALL_PREFIX})
  message(WARNING "Compiling for install workspace (${CMAKE_INSTALL_PREFIX})")
  catkin_package(
    # INCLUDE_DIRS include/pybind11_catkin
    LIBRARIES ${PYTHON_LIBRARIES}
    CATKIN_DEPENDS
    CFG_EXTRAS pybind.cmake
  )
else()
  message(WARNING "Compiling for devel workspace")
  file(MAKE_DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME})
  catkin_package(
    INCLUDE_DIRS ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME} ${EIGEN3_INCLUDE_DIR}
    LIBRARIES ${PYTHON_LIBRARIES}
    CFG_EXTRAS pybind.cmake
  )
endif()

install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_INCLUDE_DESTINATION}
)
install(
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
