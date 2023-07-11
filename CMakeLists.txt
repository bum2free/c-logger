cmake_minimum_required(VERSION 2.8.11)
project(logger C)

if (NOT "${CMAKE_SYSTEM_NAME}" STREQUAL "QNX")
set(CMAKE_C_FLAGS "-Wall -std=c89")
endif()
set(CMAKE_C_FLAGS_RELEASE "-O2 -DNDEBUG")
set(CMAKE_C_FLAGS_DEBUG "-g")

option(build_tests "Build all of own tests" OFF)
option(build_examples "Build example programs" OFF)
option(build_docs "Build doxygen documentation" OFF)

### Library
set(source_files
    src/logger.c
    src/loggerconf.c
)
# shared and static libraries
if (NOT CLOGGER_STATIC_ONLY)
add_library(${PROJECT_NAME} SHARED ${source_files})
endif()
add_library(${PROJECT_NAME}_static STATIC ${source_files})
set_target_properties(${PROJECT_NAME}_static PROPERTIES OUTPUT_NAME ${PROJECT_NAME})

# Export the include directory
if (NOT CLOGGER_STATIC_ONLY)
target_include_directories(
    ${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
endif()
target_include_directories(
    ${PROJECT_NAME}_static PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

### Install
if (NOT CLOGGER_STATIC_ONLY)
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
endif()
file(GLOB header_files src/*.h)
install(FILES ${header_files} DESTINATION ${CMAKE_INSTALL_PREFIX}/include)

### Document
if(build_docs)
    add_custom_target(doc COMMAND "doxygen" "${PROJECT_SOURCE_DIR}/doc/Doxyfile")
endif()

### Test
if(build_tests)
    enable_testing()
    add_subdirectory(test)
endif()

### Example
if(build_examples)
    add_subdirectory(example)
endif()
