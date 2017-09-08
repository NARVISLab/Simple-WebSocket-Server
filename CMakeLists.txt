cmake_minimum_required (VERSION 2.8)
project (Simple-WebSocket-Server)
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11 -Wall -Wextra -Wsign-conversion")

# Late 2017 TODO: remove the following checks and always use std::regex
if("${CMAKE_CXX_COMPILER_ID}" STREQUAL "GNU")
    if (CMAKE_CXX_COMPILER_VERSION VERSION_LESS 4.9)
        set(BOOST_COMPONENTS ${BOOST_COMPONENTS} regex)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DUSE_BOOST_REGEX")
    endif()
endif()

# Asio Standalone
include_directories(SYSTEM ${CMAKE_CURRENT_SOURCE_DIR}/../asio-1.10.6/include)
list(APPEND ASIO_DEFS "-DASIO_STANDALONE" "-DUSE_STANDALONE_ASIO" "-DASIO_HAS_STD_ADDRESSOF" "-DASIO_HAS_STD_ARRAY" "-DASIO_HAS_CSTDINT" "-DASIO_HAS_STD_SHARED_PTR" "-DASIO_HAS_STD_TYPE_TRAITS" "-D_WIN32_WINNT=0x0501")
add_definitions(${ASIO_DEFS})

if(APPLE)
  set(OPENSSL_ROOT_DIR "/usr/local/opt/openssl")
endif()

#TODO: add requirement for version 1.0.1g (can it be done in one line?)
find_package(OpenSSL REQUIRED)
include_directories(${OPENSSL_INCLUDE_DIR})

find_package(Threads REQUIRED)

include_directories(.)

add_executable(ws_examples ws_examples.cpp)
target_link_libraries(ws_examples ${OPENSSL_CRYPTO_LIBRARY})
target_link_libraries(ws_examples ${CMAKE_THREAD_LIBS_INIT})

add_executable(wss_examples wss_examples.cpp)
target_link_libraries(wss_examples ${OPENSSL_LIBRARIES})
target_link_libraries(wss_examples ${CMAKE_THREAD_LIBS_INIT})

if(MSYS)
    target_link_libraries(ws_examples ws2_32 wsock32)
    target_link_libraries(wss_examples ws2_32 wsock32)
endif()

enable_testing()
add_subdirectory(tests)
