cmake_minimum_required(VERSION 2.6)

include_directories(.)

IF(WIN32)
  ADD_DEFINITIONS(-DBOOST_USE_WINDOWS_H)
ENDIF(WIN32)

FILE(GLOB BOOST_SRC
  libs/chrono/src/*.cpp
  libs/date_time/src/*/*.cpp
  libs/exception/src/*.cpp
  libs/filesystem/src/*.cpp
  libs/system/src/*.cpp
  libs/serialization/src/*.cpp
  libs/thread/src/*.cpp
  libs/coroutine/src/*.cpp
  libs/iostreams/src/zlib.cpp
  libs/iostreams/src/file_descriptor.cpp
  libs/program_options/src/*.cpp
)

add_definitions(
  -DBOOST_ALL_NO_LIB
  -DBOOST_SYSTEM_NO_DEPRECATED
  -Dcpp_main=main
)

IF(WIN32)
  add_definitions(
    -DBOOST_THREAD_BUILD_LIB
  )
  FILE(GLOB BOOST_THREAD_SRC
    libs/thread/src/win32/*.cpp
  )
  FILE(GLOB BOOST_CONTEXT_SRC
    libs/context/src/windows/*.cpp
  )
  list(APPEND BOOST_SRC
    ${BOOST_THREAD_SRC}
    # ${BOOST_CONTEXT_SRC}
  )
ELSE()
  add_definitions(
    -DBOOST_THREAD_BUILD_LIB
  )
  FILE(GLOB BOOST_THREAD_SRC
    libs/thread/src/pthread/*.cpp
  )
  FILE(GLOB BOOST_CONTEXT_SRC
    libs/context/src/posix/*.cpp
  )
  list(APPEND BOOST_SRC
    ${BOOST_THREAD_SRC}
    # ${BOOST_CONTEXT_SRC}
  )
ENDIF()

list(REMOVE_ITEM BOOST_THREAD_SRC
  ${CMAKE_CURRENT_LIST_DIR}/libs/thread/src/pthread/once.cpp
  libs/thread/src/pthread/once.cpp
)
list(REMOVE_ITEM BOOST_SRC
  ${CMAKE_CURRENT_LIST_DIR}/libs/thread/src/pthread/once.cpp
  libs/thread/src/pthread/once.cpp
  ${CMAKE_CURRENT_LIST_DIR}/libs/detail/utf8_codecvt_facet.cpp
  libs/detail/utf8_codecvt_facet.cpp
)

add_library(boost
  STATIC
  ${BOOST_SRC}
)
