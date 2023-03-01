cmake_minimum_required(VERSION 3.4)
project(pybind11_catkin)

find_package(catkin REQUIRED)

set(PYBIND11_MINIMUM_REQUIRED_VERSION 2.5.0)
set(PYBIND11_PYTHON_VERSION ${PYTHON_VERSION_STRING})

find_package(pybind11 ${PYBIND11_MINIMUM_REQUIRED_VERSION} QUIET)
if(NOT pybind11_FOUND)
  include(ExternalProject)

  ExternalProject_Add(pybind11_src
    URL "https://github.com/pybind/pybind11/archive/v2.10.3.zip"
    UPDATE_COMMAND ""
    CMAKE_ARGS -DPYBIND11_NOPYTHON=TRUE
              -DPYBIND11_TEST=OFF
              -DPYBIND11_INSTALL=ON
              -DCMAKE_INSTALL_PREFIX=${PROJECT_BINARY_DIR}/install
    # Workaround if DESTDIR is set
    # See https://gitlab.kitware.com/cmake/cmake/-/issues/18165.
    INSTALL_COMMAND ${CMAKE_MAKE_PROGRAM} DESTDIR= install
  )

  # Copy cmake/pybind11 and include/pybind11 to corresponding devel space folders
  ExternalProject_Add_Step(pybind11_src CopyToDevel
    COMMENT "Copying to devel"
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/share/cmake/pybind11 ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
    COMMAND ${CMAKE_COMMAND} -E copy_directory ${PROJECT_BINARY_DIR}/install/include/pybind11 ${CATKIN_DEVEL_PREFIX}/include/${PROJECT_NAME}/pybind11
    DEPENDEES install
  )
endif()

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
  DIRECTORY ${CATKIN_DEVEL_PREFIX}/share/${PROJECT_NAME}/cmake
  DESTINATION ${CATKIN_PACKAGE_SHARE_DESTINATION}
)
