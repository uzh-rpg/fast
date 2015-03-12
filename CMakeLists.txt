SET(PROJECT_NAME fast)
PROJECT(${PROJECT_NAME})
CMAKE_MINIMUM_REQUIRED (VERSION 2.8)

SET( CMAKE_BUILD_TYPE Release ) 

SET(CMAKE_CXX_FLAGS "-std=c++11 -Wall -Werror -Wno-unused-variable -Wno-unused-but-set-variable -Wno-unknown-pragmas")

IF(DEFINED ENV{ARM_ARCHITECTURE})
	SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -mfpu=neon -march=armv7-a")
ELSE()
	SET(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS} -O3 -mmmx -msse -msse -msse2 -msse3 -mssse3 -fomit-frame-pointer")
ENDIF()

FIND_PACKAGE(catkin REQUIRED COMPONENTS
	# nothing
)

catkin_package(
  DEPENDS 
  CATKIN_DEPENDS 
  INCLUDE_DIRS include 
  LIBRARIES fast
)

# FAST library
SET(SOURCES
	# src/fast_9.cpp
	src/fast_10.cpp
	src/fast_10_score.cpp
	src/nonmax_3x3.cpp
	# include/fast/corner_9.h
	include/fast/corner_10.h
	include/fast/fast.h
	include/fast/faster_corner_utilities.h
	#${PROJECT_SOURCE_DIR}/src/nonmax_5x5.cpp
)
# IF(DEFINED ENV{ARM_ARCHITECTURE})
#   LIST(APPEND SOURCES ${PROJECT_SOURCE_DIR}/src/faster_corner_9_neon.cpp)
# ELSE()
  LIST(APPEND SOURCES ${PROJECT_SOURCE_DIR}/src/faster_corner_10_sse.cpp)
# ENDIF()


ADD_LIBRARY( fast SHARED ${SOURCES} )

INCLUDE_DIRECTORIES(
	include
)

# # Build a test program for the fast detector
# FIND_PACKAGE( OpenCV REQUIRED )
# ADD_EXECUTABLE( test_fast ${PROJECT_SOURCE_DIR}/test/test.cpp )
# TARGET_LINK_LIBRARIES( test_fast fast ${OpenCV_LIBS} )





