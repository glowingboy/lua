#build lua 5.1.5 for WIN32 linux

cmake_minimum_required(VERSION 3.0)
project(lua)

# if(NOT CMAKE_C_COMPILER STREQUAL "MSVC" OR NOT CMAKE_CXX_COMPILER STREQUAL "MSVC")
#   message(FATAL_ERROR "compiler is not MSVC")
# endif()

option(gbLUASTATIC "build a static lib?" ON)

# for MSVC, should use "cmake --build . --config Release" to build a release version
set(CMAKE_BUILD_TYPE "Release")
# set(src_files)
# set(header_files)

# macro(add_src name)
#   #src
#   set(src_file "src/"${name}.c)
#   if(EXISTS ${PROJECT_SOURCE_DIR}/${src_file})
#     message("file@" ${src_file} " added")
#     set(src_files ${src_files} ${src_file})
#   else()
#     message("file@" ${src_file} "not found")
#   endif()

#   #header
#   set(header_file "src/"${name}.h)
#   if(EXISTS ${PROJECT_SOURCE_DIR}/${header_file})
#     message("file@" ${header_file} " added")
#     set(header_files ${header_files} ${header_file})
#   else()
#     message("file@" ${header_file} "not found")
#   endif()

# endmacro()

# set(srcs
#   lapi
#   lauxlib
#   lbaselib
#   lcode
#   ldblib
#   ldebug
#   ldo
#   ldump
#   lfunc
#   lgc
#   linit
#   liolib
#   llex
#   llimits
#   lmathlib
#   lmem
#   loadlib
#   lobject
#   lopcodes
#   )


set(lib_srcs)

file(GLOB lib_srcs RELATIVE ${PROJECT_SOURCE_DIR}/src "src/*.c" "src/*.h")
list(REMOVE_ITEM lib_srcs "lua.c" "luac.c")

set(lib_srcs_fullpath)
foreach(lib_src ${lib_srcs})
  set(lib_src_fullpath ${PROJECT_SOURCE_DIR}/src/${lib_src})
  set(lib_srcs_fullpath ${lib_srcs_fullpath} ${lib_src_fullpath})
  message("src@" ${lib_src_fullpath} " added")
endforeach()

if(gbLUASTATIC)
  add_library(lua STATIC ${lib_srcs_fullpath})
else()
  add_library(lua SHARED ${lib_srcs_fullpath})
endif()

if(UNIX)
  find_library(libm NAMES m)

  target_link_libraries(lua ${libm})
endif()



add_executable(lua_interpreter ${PROJECT_SOURCE_DIR}/src/lua.c ${PROJECT_SOURCE_DIR}/src/lua.h)
target_link_libraries(lua_interpreter lua)

add_executable(lua_compiler ${PROJECT_SOURCE_DIR}/src/luac.c ${PROJECT_SOURCE_DIR}/src/lua.h)
target_link_libraries(lua_compiler lua)


# INSTALL

message("install dir:" " ${CMAKE_INSTALL_PREFIX}")

# lib lua
install(TARGETS lua
  ARCHIVE DESTINATION lib
  )

# headers
set(lib_headers)
foreach(src ${lib_srcs_fullpath})
  if(src MATCHES "[a-zA-Z0-9]*\\.h")
    set(lib_headers ${lib_headers} ${src})
  endif()
endforeach()

install(FILES ${lib_headers} DESTINATION include)


# set environment variables
# if(WIN32)
#   add_custom_target(gb_set_env ALL ${CMAKE_COMMAND} -E env LUA_ROOT=${CMAKE_INSTALL_PREFIX} setx LUA_ROOT ${CMAKE_INSTALL_PREFIX})
# else()
#   add_custom_target(gb_set_env ALL ${CMAKE_COMMAND} -E env LUA_ROOT=${CMAKE_INSTALL_PREFIX} export LUA_ROOT=${CMAKE_INSTALL_PREFIX})
# endif()
