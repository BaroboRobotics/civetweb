# STK Communications library
# Barobo, Inc.

cmake_minimum_required(VERSION 3.2.1)

set(target civetweb)

project(${target})

if(TARGET ${target})
  # FIXME make sure that the current target's version or configuration matches?
  # this feels rather dangerous. find_package would solve this conundrum :/
  message(FATAL_ERROR "civetweb already configured")
endif()

set(VERSION_MAJOR 1)
set(VERSION_MINOR 6)
set(VERSION_PATCH 0)
set(VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH})

# Targets and properties

if(MSVC)
  # Disable warnings:
  # C4057: ... differs in indirection to slightly different base types from ...
  # C4245: signed/unsigned mismatch
  # C4244: possible loss of data
  set_source_files_properties(../src/civetweb.c
    PROPERTIES COMPILE_FLAGS "/wd4057 /wd4245 /wd4244"
    )
endif()

add_library(${target}
  ../src/civetweb.c ../src/CivetServer.cpp)

set_target_properties(${target} PROPERTIES
  VERSION ${VERSION_MAJOR}.${VERSION_MINOR}.${VERSION_PATCH}
  SOVERSION ${VERSION_MAJOR}
  MACOSX_RPATH ON
  CXX_STANDARD 11
  CXX_STANDARD_REQUIRED ON
)

target_include_directories(${target} PUBLIC ../include)

if(WIN32)
  target_compile_definitions(${target}
    PRIVATE "-D_WIN32_WINNT=0x0502 -DWIN32_LEAN_AND_MEAN"
  )
  target_link_libraries(${target} ws2_32)
else()
  target_link_libraries(${target} pthread)
endif()

