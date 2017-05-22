cmake_minimum_required (VERSION 2.6)
project (sodiumpp)

set(CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake/modules")
include(FindSodium)
include_directories(${SODIUM_INCLUDE_DIRS})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")

set(CMAKE_CXX_FLAGS_DEBUG "${CMAKE_CXX_FLAGS_DEBUG} -O0 -g")
set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_CXX_FLAGS_RELEASE} -O2 -g")

if(SODIUMPP_MACPORTS)
    include_directories("/opt/local/include")
    link_directories("/opt/local/lib")
endif()

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/sodiumpp/include)

if(SODIUMPP_STATIC)
	add_library(sodiumpp STATIC sodiumpp/sodiumpp.cpp sodiumpp/z85/z85.c sodiumpp/z85/z85_impl.cpp)
    set(SODIUMLIB ${SODIUM_LIBRARIES})
else()
	add_library(sodiumpp SHARED sodiumpp/sodiumpp.cpp sodiumpp/z85/z85.c sodiumpp/z85/z85_impl.cpp)
    target_link_libraries(sodiumpp sodium)
    set(SODIUMLIB ${SODIUM_SHARED_LIBRARIES})
endif()

if(SODIUMPP_EXAMPLE)
	add_executable(example sodiumpp/example.cpp)
    target_link_libraries(example sodiumpp ${SODIUMLIB})
endif()

if(SODIUMPP_TEST)
    add_executable(tests sodiumpp/test.cpp)
    target_link_libraries(tests sodiumpp ${SODIUMLIB})
endif()

install(DIRECTORY sodiumpp/include/sodiumpp DESTINATION include)
install(TARGETS sodiumpp DESTINATION lib)
