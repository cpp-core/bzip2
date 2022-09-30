cmake_minimum_required (VERSION 3.24 FATAL_ERROR)
project (libbz2 VERSION 0.1 LANGUAGES C CXX)

# Make including this file idempotent
#
if(TARGET libbz2 OR TARGET libbz2::libbz2)
  return()
endif()

# Configuration variables.
#
# Build the tests and set directories if this is the master project
#
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(LIBBZ2_TEST ON CACHE BOOL "Generate the tests.")
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
  set(CMAKE_DOCS_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/docs)
  
  # Have cmake output the compilation commands and create a link to it
  # from the project root directory so the language server can find
  # it.
  #
  set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
  execute_process(COMMAND ${CMAKE_COMMAND} -E create_symlink
    ${CMAKE_BINARY_DIR}/compile_commands.json
    ${CMAKE_SOURCE_DIR}/compile_commands.json)
endif()

# Options for generating tests and documentation.
#
option(LIBBZ2_TEST "Generate the tests." OFF)
option(LIBBZ2_DOCS "Generate the docs." OFF)

# Output configuration
#
message("-- libbz2: Configuration: ${CMAKE_CURRENT_SOURCE_DIR}")
message("-- libbz2: Included from: ${CMAKE_SOURCE_DIR}")
message("-- libbz2: LIBBZ2_TEST ${LIBBZ2_TEST}")
message("-- libbz2: LIBBZ2_DOCS ${LIBBZ2_DOCS}")
message("-- libbz2: CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}")

# Setup the compilation environment before bringing in the dependencies.
#
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_compile_options(-Wall)
add_compile_options(-stdlib=libc++)
add_compile_options(-O)
add_link_options(-stdlib=libc++)

# Add our dependencies
#

# Build the library
#
file(GLOB C_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.c)
file(GLOB H_FILES RELATIVE ${CMAKE_CURRENT_SOURCE_DIR} *.h)

add_library(libbz2 ${C_FILES})
add_library(libbz2::libbz2 ALIAS libbz2)
target_sources(libbz2 PUBLIC FILE_SET HEADERS BASE_DIRS HEADER_FILES ${H_FILES})

# Optionally configure the tests
#
#if(LIBBZ2_TEST)
#  enable_testing()
#  add_subdirectory(test)
#endif()

# Optionally configure the documentation
#
# if(LIBBZ2_DOCS)
#   add_subdirectory(docs)
# endif()

# Installation
#
include(${CMAKE_CURRENT_SOURCE_DIR}/cmake/install_config.cmake)
install_config(libbz2)
