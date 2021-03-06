project(matrix_malos)
cmake_minimum_required(VERSION 2.8)

add_definitions(-std=c++11)

include (../cmake/FatalWarnings.cmake)
ADM_EXTRA_WARNINGS()

find_package(Protobuf 3 REQUIRED)
find_package(Threads REQUIRED)

# This won't find ZQM in custom locations.
# FIXME(nelson.castillo): Improve.
FIND_LIBRARY(ZMQ_LIB NAMES zmq)
FIND_PATH(ZMQ_INCLUDE_DIR NAMES zmq.hpp)

FIND_LIBRARY(MATRIX_PROTOS_LIB NAMES matrixio_protos)

message(STATUS "ZMQ found => " "${ZMQ_LIB}")
message(STATUS "ZMQ_INCLUDE found => " "${ZMQ_INCLUDE_DIR}")
message(STATUS "MATRIX_PROTOS_LIB found => " "${MATRIX_PROTOS_LIB}")
message(STATUS "MATRIX_PROTOS_INCLUDE_DIR found => " "${DMATRIX_PROTOS_INCLUDE_DIR}")
message(STATUS "Protobuf LIB found => " "${PROTOBUF_LIBRARY}")
message(STATUS "Protobuf INCLUDE found => " "${PROTOBUF_INCLUDE_DIR}")


set(matrix_malos_zmq_src
  zmq_push.h
  zmq_push.cpp
  zmq_pull.h
  zmq_pull.cpp
)

set(CMAKE_INCLUDE_CURRENT_DIR ON)
add_library(matrix_malos_zmq ${matrix_malos_zmq_src})
set_property(TARGET matrix_malos_zmq PROPERTY CXX_STANDARD 11)
target_link_libraries(matrix_malos_zmq ${ZMQ_LIB} ${CMAKE_THREAD_LIBS_INIT})

set(matrix_malos_src
  malos_base.cpp malos_base.h
  driver_manager.cpp driver_manager.h
)

add_library(matrix_malos ${matrix_malos_src})
  set_property(TARGET matrix_malos PROPERTY CXX_STANDARD 11)
  target_link_libraries(matrix_malos matrix_malos_zmq.dll)
  target_link_libraries(matrix_malos ${MATRIX_PROTOS_LIB})
  target_link_libraries(matrix_malos ${CMAKE_THREAD_LIBS_INIT})

include_directories(${ZMQ_INCLUDE_DIR})
include_directories(${MATRIX_PROTOS_INCLUDE_DIR})
include_directories(${PROTOBUF_INCLUDE_DIR})

# Binary to be installed.
install(TARGETS matrix_malos_zmq DESTINATION lib)
install(TARGETS matrix_malos DESTINATION lib)

set (matrix_malos_headers
  malos_base.h
  driver_manager.h
  zmq_pull.h
  zmq_push.h
)

install (FILES ${matrix_malos_headers} DESTINATION include/matrix_malos)
install (FILES third_party/zmq.hpp  DESTINATION include/matrix_malos/third_party)





