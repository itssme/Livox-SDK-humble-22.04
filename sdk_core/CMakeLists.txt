cmake_minimum_required(VERSION 3.0)

project(livox_sdk)

# Version info
set(LIVOX_SDK_MAJOR_VERSION "2")
set(LIVOX_SDK_MINOR_VERSION "3")
set(LIVOX_SDK_PATCH_VERSION "0")
set(LIVOX_SDK_VERSION_STRING "${LIVOX_SDK_MAJOR_VERSION}.${LIVOX_SDK_MINOR_VERSION}.${LIVOX_SDK_PATCH_VERSION}")

# Platform check
if(WIN32)
  set(PLATFORM win)
else()
  set(PLATFORM unix)
endif()

# Common sources
set(LIVOX_SDK_SOURCES
    src/third_party/FastCRC/FastCRC_tables.hpp
    src/third_party/FastCRC/FastCRCsw.cpp
    src/base/io_loop.h
    src/base/io_loop.cpp
    src/base/thread_base.h
    src/base/thread_base.cpp
    src/base/io_thread.h
    src/base/io_thread.cpp
    src/base/network/network_util.h
    src/base/network/${PLATFORM}/network_util.cpp
    src/base/logging.h
    src/base/logging.cpp
    src/base/noncopyable.h
    src/livox_sdk.cpp
    src/device_discovery.h
    src/device_discovery.cpp
    src/device_manager.h
    src/device_manager.cpp
    src/comm/comm_port.cpp
    src/comm/sdk_protocol.h
    src/comm/sdk_protocol.cpp
    src/data_handler/data_handler.h
    src/data_handler/data_handler.cpp
    src/data_handler/hub_data_handler.h
    src/data_handler/hub_data_handler.cpp
    src/data_handler/lidar_data_handler.h
    src/data_handler/lidar_data_handler.cpp
    src/command_handler/command_handler.h
    src/command_handler/command_handler.cpp
    src/command_handler/command_channel.h
    src/command_handler/command_channel.cpp
    src/command_handler/hub_command_handler.h
    src/command_handler/hub_command_handler.cpp
    src/command_handler/lidar_command_handler.h
    src/command_handler/lidar_command_handler.cpp
    src/command_handler/command_impl.cpp
    src/command_handler/command_impl.h
    src/base/multiple_io/multiple_io_base.h
    src/base/multiple_io/multiple_io_base.cpp
    src/base/multiple_io/multiple_io_factory.h
    src/base/multiple_io/multiple_io_epoll.h
    src/base/multiple_io/multiple_io_epoll.cpp
    src/base/multiple_io/multiple_io_poll.h
    src/base/multiple_io/multiple_io_poll.cpp
    src/base/multiple_io/multiple_io_select.h
    src/base/multiple_io/multiple_io_select.cpp
    src/base/multiple_io/multiple_io_kqueue.h
    src/base/multiple_io/multiple_io_kqueue.cpp
    src/base/wake_up/wake_up_pipe.h
    src/base/wake_up/${PLATFORM}/wake_up_pipe.cpp
)

# Public headers
set(LIVOX_SDK_PUBLIC_HEADERS
    include/livox_def.h
    include/livox_sdk.h
)

# Include directories
include_directories(
    include
    include/third_party/FastCRC
    include/third_party/spdlog
    include/third_party/cmdline
    src
)

# === Static Library ===
add_library(livox_sdk_static STATIC ${LIVOX_SDK_SOURCES})
set_target_properties(livox_sdk_static PROPERTIES OUTPUT_NAME livox_sdk)
set_target_properties(livox_sdk_static PROPERTIES PUBLIC_HEADER "${LIVOX_SDK_PUBLIC_HEADERS}")

# === Shared Library ===
add_library(livox_sdk_shared SHARED ${LIVOX_SDK_SOURCES})
set_target_properties(livox_sdk_shared PROPERTIES OUTPUT_NAME livox_sdk)
set_target_properties(livox_sdk_shared PROPERTIES VERSION ${LIVOX_SDK_VERSION_STRING} SOVERSION ${LIVOX_SDK_MAJOR_VERSION})
set_target_properties(livox_sdk_shared PROPERTIES PUBLIC_HEADER "${LIVOX_SDK_PUBLIC_HEADERS}")

# Compiler flags
foreach(TARGET livox_sdk_static livox_sdk_shared)
    target_compile_options(${TARGET}
        PRIVATE $<$<CXX_COMPILER_ID:GNU>:-Wall -Werror -Wno-c++11-long-long -Wno-c++20-compat>
        PRIVATE $<$<CXX_COMPILER_ID:AppleClang>:-Wno-unknown-pragmas -Wall -Werror -Wno-c++11-long-long -Wno-c++20-compat>
        PRIVATE $<$<CXX_COMPILER_ID:Clang>:-Wno-unknown-pragmas -Wall -Werror -Wno-c++11-long-long -Wno-c++20-compat>)
endforeach()

# Install
install(TARGETS livox_sdk_shared
        COMPONENT sdk
        ARCHIVE DESTINATION lib
        LIBRARY DESTINATION lib
        PUBLIC_HEADER DESTINATION include)

install(TARGETS livox_sdk_static
        COMPONENT sdk
        ARCHIVE DESTINATION lib
        PUBLIC_HEADER DESTINATION include)