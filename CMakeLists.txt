CMAKE_MINIMUM_REQUIRED(VERSION 3.0.0)
project(yolov8_onnxruntime)
add_compile_options(-std=c++17)

# set build type
IF(NOT CMAKE_BUILD_TYPE)
  SET(CMAKE_BUILD_TYPE Release)
ENDIF()

SET(ONNXRUNTIME_DIR /opt/onnxruntime)  # onnxruntime root

# findpackage openvino
find_package(OpenVINO REQUIRED)

# make sure this dir exists
IF(NOT EXISTS ${ONNXRUNTIME_DIR})
  MESSAGE(FATAL_ERROR "ONNXRUNTIME_DIR not found")
ENDIF()

find_package(OpenCV REQUIRED)

SET(CMAKE_CXX_STANDARD 17)
SET(CMAKE_CXX_STANDARD_REQUIRED ON)

include_directories(
  include
  ${OpenCV_INCLUDE_DIRS}
  ${ONNXRUNTIME_DIR}/include
)

set (${PROJECT_NAME}_CPP_SOURCES
src/nn/autobackend.cpp
src/nn/onnx_model_base.cpp 
src/utils/augment.cpp
src/utils/common.cpp
src/utils/ops.cpp
)

add_library(${PROJECT_NAME} SHARED ${${PROJECT_NAME}_CPP_SOURCES})
# add_dependencies(${PROJECT_NAME} ${${PROJECT_NAME}_EXPORTED_TARGETS})
target_link_libraries(${PROJECT_NAME} PUBLIC ${OpenCV_LIBS} ${ONNXRUNTIME_DIR}/lib/libonnxruntime.so )

add_executable(${PROJECT_NAME}_test src/main.cpp)
target_link_libraries(${PROJECT_NAME}_test ${PROJECT_NAME} ${OpenCV_LIBS} )