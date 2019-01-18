# CMake file for use in conjunction with scikit-build

cmake_minimum_required(VERSION 3.4.0)

if (CMAKE_VERSION VERSION_GREATER "3.11.99")
  cmake_policy(SET CMP0074 NEW)
endif()

project(slycot VERSION ${SLYCOT_VERSION})

# Fortran detection fails on windows, use the CMAKE_C_SIMULATE flag to
# force success
if(WIN32)
  set(CMAKE_Fortran_SIMULATE_VERSION 19.0)
#  set(CMAKE_Fortran_COMPILER_FORCED TRUE)
# set(CMAKE_C_COMPILER_VERSION 19.0)
endif()

enable_language(C)
enable_language(Fortran)

find_package(PythonLibs REQUIRED)
find_package(NumPy REQUIRED)
#set(BLA_VENDOR "OpenBLAS")
find_package(LAPACK REQUIRED)
message(STATUS "LAPACK: ${LAPACK_LIBRARIES}")
message(STATUS "Slycot version: ${SLYCOT_VERSION}")

# find python, standard packages, F2PY find flaky on Windows
if (NOT WIN32)
  find_package(F2PY REQUIRED)
endif()

# pic option for flang not correct, remove for Windows
if (WIN32)
  set(CMAKE_Fortran_COMPILE_OPTIONS_PIC "")
endif()

# base site dir, use python installation for location specific includes
execute_process(
  COMMAND "${PYTHON_EXECUTABLE}" -c
  "from distutils.sysconfig import get_python_lib as pl; print(pl())"
  OUTPUT_VARIABLE PYTHON_SITE
  OUTPUT_STRIP_TRAILING_WHITESPACE)
if(WIN32)
  string(REPLACE "\\" "/" PYTHON_SITE ${PYTHON_SITE})
endif()

add_subdirectory(slycot)
