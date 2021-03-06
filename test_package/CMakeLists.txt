cmake_minimum_required(VERSION 3.1.2)
project(PackageTest CXX)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup(TARGETS)

if (CONAN_SETTINGS_ARCH STREQUAL "x86")
  set(CMAKE_SIZEOF_VOID_P 4)
endif()

list (FIND CONAN_DEFINES_MONGO-C-DRIVER "-DBSON_STATIC" _index)
if (${_index} GREATER -1)
  set(LIB_SUFFIX _STATIC)
  find_package(libmongoc-static-1.0 REQUIRED)
  find_package(libbson-static-1.0 REQUIRED)
else()
  find_package(libmongoc-1.0 REQUIRED)
  find_package(libbson-1.0 REQUIRED)
endif()

message ("--   mongoc found version \"${MONGOC${LIB_SUFFIX}_VERSION}\"")
message ("--   mongoc include path \"${MONGOC${LIB_SUFFIX}_INCLUDE_DIRS}\"")
message ("--   mongoc libraries \"${MONGOC${LIB_SUFFIX}_LIBRARIES}\"")

add_executable(example example.cpp)
target_include_directories(example PRIVATE ${MONGOC${LIB_SUFFIX}_INCLUDE_DIRS})
target_link_libraries(example ${MONGOC${LIB_SUFFIX}_LIBRARIES})
target_compile_definitions(example PRIVATE ${MONGOC${LIB_SUFFIX}_DEFINITIONS})


add_executable(example2 example.cpp)
target_link_libraries(example2 CONAN_PKG::mongo-c-driver)


