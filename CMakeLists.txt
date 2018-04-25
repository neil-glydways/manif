cmake_minimum_required(VERSION 2.8.3)
project(manif)

# Check that the compoiler has c++11 support
# Crash otherwise.
include(CheckCXXCompilerFlag)
CHECK_CXX_COMPILER_FLAG("-std=c++11" COMPILER_SUPPORTS_CXX11)
if(COMPILER_SUPPORTS_CXX11)
  message(STATUS "The compiler ${CMAKE_CXX_COMPILER} has C++11 support.")
else()
  message(FATAL_ERROR "The compiler ${CMAKE_CXX_COMPILER} has no C++11 support. Please use a different C++ compiler.")
endif()

#find_package(catkin REQUIRED)

find_package(cmake_modules REQUIRED)
find_package(Eigen REQUIRED)
find_package(Threads REQUIRED)

add_subdirectory(external/lspdlog)

# Options. Turn on with 'cmake -DBUILD_TESTING=ON'.
# catkin build manif --cmake-args -DBUILD_TESTING=ON
option(BUILD_TESTING "Build all tests." OFF)

###################################
## catkin specific configuration ##
###################################
#catkin_package(
#  INCLUDE_DIRS include #${EIGEN_INCLUDE_DIRS}
##  LIBRARIES ${PROJECT_NAME}
##  CATKIN_DEPENDS other_catkin_pkg
#  DEPENDS Eigen
#)

###########
## Build ##
###########

include_directories(include)
include_directories(SYSTEM
  ${EIGEN_INCLUDE_DIRS}
  ${LSPDLOG_INCLUDE_DIRS}
)

add_executable(dummy src/dummy.cpp)
target_link_libraries(dummy ${catkin_LIBRARIES})
add_dependencies(dummy spdlog)
target_link_libraries(dummy ${CMAKE_THREAD_LIBS_INIT})

if(CMAKE_VERSION VERSION_LESS "3.1")
  set_target_properties(dummy PROPERTIES COMPILE_FLAGS "-std=c++11")
else()
  set_property(TARGET dummy PROPERTY CXX_STANDARD 11)
  set_property(TARGET dummy PROPERTY CXX_STANDARD_REQUIRED ON)
  set_property(TARGET dummy PROPERTY CXX_EXTENSIONS OFF)
endif()

#############
## Install ##
#############

#message(WARNING ${CMAKE_INSTALL_PREFIX})

# Mark cpp header files for installation
install(DIRECTORY include/${PROJECT_NAME}
  DESTINATION include
  FILES_MATCHING PATTERN "*.h"
  PATTERN ".svn" EXCLUDE
)

#############
## Testing ##
#############

if (BUILD_TESTING)

  enable_testing()

  find_package(GTest REQUIRED)
  include_directories(${GTEST_INCLUDE_DIRS})

  message(WARNING "DERERERERERE")

  add_subdirectory(test)

endif(BUILD_TESTING)