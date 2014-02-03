#######################################
# user config

SET(IS_ARM FALSE)
SET(BUILD_TEST TRUE)

#######################################

CMAKE_MINIMUM_REQUIRED (VERSION 2.8.3)
PROJECT(fast)

# Build type and flags
SET(CMAKE_BUILD_TYPE Release) # Options:  Debug, RelWithDebInfo, Release
SET(CMAKE_VERBOSE_MAKEFILE ON)
SET(CMAKE_CXX_FLAGS "-Wall -Werror -Wno-unused-variable -Wno-unused-but-set-variable -Wno-unknown-pragmas")
SET(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS} -O0 -g")
IF(IS_ARM)
  SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -mfpu=neon -march=armv7-a")
ELSE()
  SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -mmmx -msse -msse -msse2 -msse3 -mssse3 -fomit-frame-pointer")
ENDIF()

# Add Catkin
FIND_PACKAGE(catkin REQUIRED)

# Add Sources
SET(SOURCES  ${PROJECT_SOURCE_DIR}/src/fast_10.cpp
             ${PROJECT_SOURCE_DIR}/src/fast_10_score.cpp
             ${PROJECT_SOURCE_DIR}/src/nonmax_3x3.cpp)
IF(IS_ARM)
  LIST(APPEND SOURCES ${PROJECT_SOURCE_DIR}/src/faster_corner_9_neon.cpp)
ELSE()
  LIST(APPEND SOURCES ${PROJECT_SOURCE_DIR}/src/faster_corner_10_sse.cpp)
ENDIF()

# Add library
INCLUDE_DIRECTORIES(include src ${catkin_INCLUDE_DIRS})
ADD_LIBRARY(fast SHARED ${SOURCES})

# Export the dependencis of this package
catkin_package(
  INCLUDE_DIRS include src
  LIBRARIES fast
)

# Add Tests
IF(BUILD_TEST)
  MESSAGE("Building Fast Test")
  FIND_PACKAGE(OpenCV REQUIRED)
  ADD_DEFINITIONS(-DTEST_DATA_DIR=\"${PROJECT_SOURCE_DIR}/test/data\")
  ADD_EXECUTABLE(fast_test test/test.cpp)
  TARGET_LINK_LIBRARIES(fast_test ${OpenCV_LIBS} fast)
ENDIF()