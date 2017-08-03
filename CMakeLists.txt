cmake_minimum_required(VERSION 2.8)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_SOURCE_DIR}/cmake/Modules)

project(shipgate_server)
set(shipgate_server_VERSION_MAJOR 0)
set(shipgate_server_VERSION_MINOR 1)
set(shipgate_server_VERSION_PATCH 0)

set(shipgate_server_VERSION_STRING "${shipgate_server_MAJOR_VERSION}.${shipgate_server_MINOR_VERSION}.${shipgate_server_PATCH_VERSION}")


option(IPV6 "build application with IPv6 Support" ON) # ON is the default

include_directories(src)

set(LIBSYLVERANT_LIBRARY sylverant)
include_directories(${CMAKE_SOURCE_DIR}/lib/libsylverant/include)

find_package(ZLIB REQUIRED)
include_directories(${ZLIB_INCLUDE_DIR})

find_package(Iconv REQUIRED)
include_directories(${ICONV_INCLUDE_DIR})

find_package(GnuTLS REQUIRED)
include_directories(${GNUTLS_INCLUDE_DIR})

IF(ICONV_SECOND_ARGUMENT_IS_CONST)
  add_definitions (-DICONV_CONST=const)
ELSE()
  add_definitions (-DICONV_CONST=)
ENDIF()

file(GLOB SOURCES src/*.c)

add_definitions (-Wall)

add_definitions (-DVERSION="${shipgate_server_VERSION_STRING}")

if(IPV6)
  add_definitions(-DENABLE_IPV6)
endif(IPV6)

add_definitions(-DVERSION_MAJOR=${shipgate_server_VERSION_MAJOR})
add_definitions(-DVERSION_MINOR=${shipgate_server_VERSION_MINOR})
add_definitions(-DVERSION_MICRO=${shipgate_server_VERSION_PATCH})


add_subdirectory(lib/libsylverant)

add_executable(sylverant-shipgate ${SOURCES})
target_link_libraries(sylverant-shipgate ${ZLIB_LIBRARY} ${GNUTLS_LIBRARIES} ${LIBSYLVERANT_LIBRARY})

if(${CMAKE_SYSTEM_NAME} STREQUAL "Linux")
    install(TARGETS sylverant-shipgate RUNTIME DESTINATION bin)
endif()