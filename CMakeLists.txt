#######################################################################
# The Embedded Template Library (https://www.etlcpp.com/)
#######################################################################
cmake_minimum_required(VERSION 3.5.0)
project(etl)

#######################################################################
# Define an option to allow consumers of the library to 
# specify the profile they would like to compile to.  If
# not explicitly set CMake will attempt to choose an appropriate
# profile based on the compiler
#######################################################################
set(ETL_PROFILE "DEFAULT" CACHE STRING "Defines what profile header to include.  See https://www.etlcpp.com/setup.html"
)

add_library(etl
  src/binary.cpp
  src/crc16_ccitt.cpp
  src/crc16_kermit.cpp
  src/crc32.cpp
  src/crc64_ecma.cpp
  src/crc8_ccitt.cpp
  src/error_handler.cpp
  src/pearson.cpp
  src/random.cpp
  src/private
  src/private/pvoidvector.cpp
  )
target_include_directories(etl
  PUBLIC
  include/etl/
  include/etl/atomic
  include/etl/profiles
  PRIVATE
  include/etl/private
  )

if (${ETL_PROFILE} STREQUAL DEFAULT)
  if (CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    set(ETL_PROFILE "PROFILE_GCC_GENERIC")

  elseif (CMAKE_CXX_COMPILER_ID MATCHES "MSVC")
    set(ETL_PROFILE "PROFILE_MSVC")
 
  elseif (CMAKE_CXX_COMPILER_ID MATCHES "ARMCC")
    if (CXX_STANDARD EQUAL 11)
      set(ETL_PROFILE "PROFILE_ARMV6")
    else ()
      set(ETL_PROFILE "PROFILE_ARMV5")
    endif()
  elseif (CMAKE_CXX_COMPILER_ID MATCHES "TI")
    set(ETL_PROFILE "PROFILE_TICC")

  else()
    message(FATAL_ERROR "Can't generate default profile for compiler: ${CMAKE_CXX_COMPILER_ID}")
  endif()
endif() 

message(STATUS "Compiling with ETL profile set to: ${ETL_PROFILE}")

target_compile_definitions(etl
  PUBLIC
  ${ETL_PROFILE} 
  )

