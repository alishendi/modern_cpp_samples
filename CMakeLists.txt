cmake_minimum_required(VERSION 3.16)
project(MyCppProject VERSION 1.0 LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# Define executable from single source file
add_executable(type_erasure type_erasure.cpp)
