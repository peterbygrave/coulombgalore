cmake_minimum_required(VERSION 3.11.4 FATAL_ERROR)

project(coulombgalore)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_CXX_STANDARD_REQUIRED YES)
set(CMAKE_CXX_EXTENSIONS NO)
enable_testing()

include(FetchContent)

## GCC
if (CMAKE_CXX_COMPILER_ID MATCHES "GNU")
    add_compile_options(-Wall -Wextra -Wpedantic -Wunreachable-code -Wstrict-aliasing)
## Clang
elseif (CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    add_compile_options(-Wall -Wextra -Wpedantic -Wunreachable-code -Wstrict-aliasing)
endif()

message("# download modern json")
FetchContent_Declare(
    modernjson
    URL "https://github.com/nlohmann/json/releases/download/v3.5.0/include.zip"
    URL_HASH SHA256=3564da9c5b0cf2e032f97c69baedf10ddbc98030c337d0327a215ea72259ea21)
FetchContent_GetProperties(modernjson)
if(NOT modernjson_POPULATED)
    FetchContent_Populate(modernjson)
endif()

message("# download Armadillo")
FetchContent_Declare(
    armadillo
    URL "https://downloads.sourceforge.net/project/arma/armadillo-10.1.2.tar.xz?r=https%3A%2F%2Fsourceforge.net%2Fprojects%2Farma%2Ffiles%2Farmadillo-10.1.2.tar.xz%2Fdownload&ts=1606475750"
    URL_HASH MD5=1419e8ef00a8e08a34a5e2e1164ef042)
FetchContent_GetProperties(armadillo)
if(NOT armadillo_POPULATED)
    FetchContent_Populate(armadillo)
endif()
message("${armadillo_SOURCE_DIR}")

message("# download doctest")
FetchContent_Declare(
    doctest
    URL "https://github.com/onqtam/doctest/archive/2.2.0.tar.gz"
    URL_MD5 3f91262877b792e545f971e54afb7602)
FetchContent_GetProperties(doctest)
if(NOT doctest_POPULATED)
    FetchContent_Populate(doctest)
    add_definitions(-DDOCTEST_CONFIG_DISABLE)
endif()

include_directories(SYSTEM ${armadillo_SOURCE_DIR}/include/ ${doctest_SOURCE_DIR} ${modernjson_SOURCE_DIR})
include_directories(${CMAKE_SOURCE_DIR})

find_package(BLAS)
find_package(LAPACK)
add_executable(example test/example.cpp)
target_link_libraries(example BLAS LAPACK)
add_executable(unittests test/unittests.cpp)
target_link_libraries(unittests BLAS LAPACK)
add_test(NAME unittests COMMAND unittests)
