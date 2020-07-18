cmake_minimum_required(VERSION 2.8.8)
cmake_policy(SET CMP0079 NEW)

# ---[ Configurations types
set(CMAKE_CONFIGURATION_TYPES "Debug;Release" CACHE STRING "Possible configurations" FORCE)
if (DEFINED CMAKE_BUILD_TYPE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS ${CMAKE_CONFIGURATION_TYPES})
endif()

set( RS2_INC_DIRS "C:/Program Files (x86)/Intel RealSense SDK 2.0/include")
set( RS2_LIB_DIRS "C:/Program Files (x86)/Intel RealSense SDK 2.0/lib/x64/realsense2.lib")

# ---[ Solution name
project(kfusion C CXX)

# ---[ utility
list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")
include(cmake/Utils.cmake)
include(cmake/Targets.cmake)

# ---[ find dependencies
find_package(OpenCV REQUIRED COMPONENTS core viz highgui)
find_package(CUDA REQUIRED)
##find_package(realsense REQUIRED)
##find_package(OpenNI REQUIRED)
include_directories(${OpenCV_INCLUDE_DIRS} ${CUDA_INCLUDE_DIRS} ${RS2_INC_DIRS})

# ---[ misc settings
if(USE_PROJECT_FOLDERS)
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)
  set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMakeTargets")
endif()




# ---[ cuda settings
set(HAVE_CUDA 1)
if (CUDA_VERSION_MAJOR GREATER 8)
  list(APPEND CUDA_NVCC_FLAGS "-gencode arch=compute_62,code=sm_62;-gencode arch=compute_70,code=sm_70;-gencode arch=compute_75,code=sm_75")
else()
  list(APPEND CUDA_NVCC_FLAGS "-gencode arch=compute_60,code=sm_60")
endif()

if(UNIX OR APPLE)
  list(APPEND CUDA_NVCC_FLAGS "-Xcompiler;-fPIC;")
endif()

warnings_disable(CMAKE_CXX_FLAGS /wd4985)

add_subdirectory(kfusion)
add_subdirectory(apps)
