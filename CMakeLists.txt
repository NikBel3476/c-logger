cmake_minimum_required(VERSION 3.0)
project(logger C)

if (MSVC)
    set(CMAKE_C_FLAGS "/Wall")
    set(CMAKE_C_FLAGS_RELEASE "/O2")
else()
    set(CMAKE_C_FLAGS "-Wall -std=c89")
    set(CMAKE_C_FLAGS_RELEASE "-O2 -DNDEBUG")
    set(CMAKE_C_FLAGS_DEBUG "-g")
endif()

option(build_tests "Build all of own tests" OFF)
option(build_examples "Build example programs" OFF)
option(build_docs "Build doxygen documentation" OFF)

### Library
set(source_files
    src/logger.c
    src/loggerconf.c
)
set(header_files
    src/logger.h
    src/loggerconf.h
)

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/src/logger.h ${PROJECT_BINARY_DIR}/logger.h)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/src/loggerconf.h ${PROJECT_BINARY_DIR}/loggerconf.h)

# shared and static libraries
add_library(${PROJECT_NAME} SHARED ${source_files})
add_library(${PROJECT_NAME}_static STATIC ${source_files})

# Export the include directory
target_include_directories(
    ${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)
target_include_directories(
    ${PROJECT_NAME}_static PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

### Install
install(TARGETS ${PROJECT_NAME} DESTINATION ${CMAKE_INSTALL_PREFIX}/lib)
install(FILES ${header_files} DESTINATION ${CMAKE_INSTALL_PREFIX}/include)

### Document
if(build_docs)
    add_custom_target(c-logger-doc COMMAND "doxygen" "${PROJECT_SOURCE_DIR}/doc/Doxyfile")
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
