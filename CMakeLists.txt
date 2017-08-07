#build lua 5.1.5 for win32

cmake_minimum_required(VERSION 3.6)
project(lua)

if(NOT CMAKE_C_COMPILER STREQUAL "MSVC" OR NOT CMAKE_CXX_COMPILER STREQUAL "MSVC")
  message(FATAL_ERROR "compiler is not MSVC")
endif()

option(gbLUASTATIC "build a static lib?" ON)

set(src_files)
set(header_files)

macro(add_src name)
  #src
  set(src_file "src/"${name}.c)
  if(EXISTS ${PROJECT_SOURCE_DIR}/${src_file})
    message("file@" ${src_file} " added")
    set(src_files ${src_files} ${src_file})
  else()
    message("file@" ${src_file} "not found")
  endif()

  #header
  set(header_file "src/"${name}.h)
  if(EXISTS ${PROJECT_SOURCE_DIR}/${header_file})
    message("file@" ${header_file} " added")
    set(header_files ${header_files} ${header_file})
  else()
    message("file@" ${header_file} "not found")
  endif()

endmacro()


