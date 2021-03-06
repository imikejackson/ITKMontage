cmake_minimum_required(VERSION 3.10.2)

if(CMAKE_CXX_STANDARD EQUAL "98" )
   message(FATAL_ERROR "CMAKE_CXX_STANDARD:STRING=98 is not supported in ITK version 5 and greater.")
endif()

if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11) # Supported values are ``11``, ``14``, and ``17``.
endif()
if(NOT CMAKE_CXX_STANDARD_REQUIRED)
  set(CMAKE_CXX_STANDARD_REQUIRED ON)
endif()
if(NOT CMAKE_CXX_EXTENSIONS)
  set(CMAKE_CXX_EXTENSIONS OFF)
endif()

project(Montage)

# Suppress warnings about potentially uninstantiated static members
if(CMAKE_CXX_COMPILER_ID MATCHES "Clang")
  include(CheckCXXCompilerFlag)
  CHECK_CXX_COMPILER_FLAG("-Wno-undefined-var-template" COMPILER_HAS_NO_UNDEFINED_VAR_TEMPLATE)
  if(COMPILER_HAS_NO_UNDEFINED_VAR_TEMPLATE)
    set(CMAKE_CXX_FLAGS "-Wno-undefined-var-template ${CMAKE_CXX_FLAGS}")
  endif()
endif()

# set(Montage_LIBRARIES Montage) # this module is header-only, it does not have compiled part

if(NOT ITK_SOURCE_DIR)
  find_package(ITK REQUIRED)
  list(APPEND CMAKE_MODULE_PATH ${ITK_CMAKE_DIR})
  # Patch igenerator for enum wrapping until ITK 5.1 RC 2 is released
  file(COPY ${CMAKE_CURRENT_SOURCE_DIR}/wrapping/igenerator.py DESTINATION
    ${ITK_CMAKE_DIR}/../Wrapping/Generators/SwigInterface/)
  include(ITKModuleExternal)
else()
  set(ITK_DIR ${CMAKE_BINARY_DIR})
  itk_module_impl()
endif()

itk_module_examples()
