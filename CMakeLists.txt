project( timerlr )
cmake_minimum_required( VERSION 2.6 )


include( CheckCXXCompilerFlag )
check_cxx_compiler_flag( "-std=c++11"   COMPILER_SUPPORTS_CXX11 )
check_cxx_compiler_flag( "-std=c++0x"   COMPILER_SUPPORTS_CXX0X )


if (${CMAKE_CXX_COMPILER_ID} MATCHES GNU)
  # Use C++11 when using GNU compilers.
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -std=c++11")
elseif (${CMAKE_CXX_COMPILER_ID} MATCHES Clang)
  # We want to link in C++11 mode in Clang too, but also set a high enough
  # template depth for the template metaprogramming.
  set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -ftemplate-depth=256 -std=c++11")
  if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
    # Use libc++ only in OS X.
    set (CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -stdlib=libc++")
    set (CMAKE_EXE_LINKER_FLAGS "${CMAKE_EXE_LINKER_FLAGS} -lc++")
  endif()
endif()


find_package( Boost 1.63.0
  REQUIRED unit_test_framework system regex date_time thread filesystem
  program_options chrono atomic log)

include_directories( ${Boost_INCLUDE_DIR} )
link_directories( ${Boost_LIBRARY_DIRS} )
set( Boost_USE_STATIC_LIBS        OFF )
set( Boost_USE_MULTITHREADED      ON )
set( Boost_USE_STATIC_RUNTIME     OFF )
set( BOOST_ALL_DYN_LINK           ON )

add_executable( ${PROJECT_NAME} main.cpp )
target_link_libraries( ${PROJECT_NAME} ${Boost_LIBRARIES} )

add_executable( asiotimermain   asiotimermain.cpp )
target_link_libraries( asiotimermain ${Boost_LIBRARIES} )
