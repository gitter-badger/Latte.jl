cmake_minimum_required (VERSION 2.8.11)
project (LatteIO)
option(LATTE_MPI "Include MPI support when building the Latte runtime" OFF)
if(LATTE_MPI)
    add_definitions(-DLATTE_BUILD_MPI)
endif(LATTE_MPI)

if(DEBUG)
    add_definitions(-DDEBUG)
endif(DEBUG)

find_package( HDF5 REQUIRED )
include_directories( ${HDF5_INCLUDE_DIRS} )

FIND_PACKAGE( OpenMP REQUIRED)

find_package(MPI)

if(LATTE_MPI)
    if(${HDF5_IS_PARALLEL})
        include_directories( ${MPI_INCLUDE_PATH})
    else()
        message( FATAL_ERROR "HDF5 must be compiled with --enable-parallel to use Latte with MPI" )
    endif()
endif()

set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} ${OpenMP_C_FLAGS}")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")

add_library(LatteIO SHARED io.cpp io.h dataset.cpp dataset.h)
target_link_libraries(LatteIO ${HDF5_LIBRARIES})

if(${HDF5_IS_PARALLEL})
    target_link_libraries(LatteIO ${MPI_C_LIBRARIES})
endif()
