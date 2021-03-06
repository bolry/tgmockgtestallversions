cmake_minimum_required(VERSION 3.9)

# Download and unpack googletest at configure time
configure_file(CMakeLists.txt.in googletest-download/CMakeLists.txt)
execute_process(COMMAND ${CMAKE_COMMAND} -G "${CMAKE_GENERATOR}" .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "CMake step for googletest failed: ${result}")
endif()
execute_process(COMMAND ${CMAKE_COMMAND} --build .
  RESULT_VARIABLE result
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}/googletest-download )
if(result)
  message(FATAL_ERROR "Build step for googletest failed: ${result}")
endif()

# Prevent overriding the parent project's compiler/linker
# settings on Windows
set(gtest_force_shared_crt ON CACHE BOOL "" FORCE)

# Add googletest directly to our build. This defines
# the gtest/gmock and gtest/gmock_main targets.
add_subdirectory(${CMAKE_BINARY_DIR}/googletest-src
                 ${CMAKE_BINARY_DIR}/googletest-build
                 EXCLUDE_FROM_ALL)

# The gtest/gtest_main targets carry header search path
# dependencies automatically when using CMake 2.8.11 or
# later. Otherwise we have to add them here ourselves.
if (CMAKE_VERSION VERSION_LESS 2.8.11)
  include_directories(BEFORE SYSTEM
    "${gtest_SOURCE_DIR}/include" "${gmock_SOURCE_DIR}/include")
else()
  target_include_directories(gmock_main SYSTEM BEFORE INTERFACE
    "${gtest_SOURCE_DIR}/include" "${gmock_SOURCE_DIR}/include")
  target_include_directories(gmock      SYSTEM BEFORE INTERFACE
    "${gtest_SOURCE_DIR}/include" "${gmock_SOURCE_DIR}/include")

  target_include_directories(gtest_main SYSTEM BEFORE INTERFACE
    "${gtest_SOURCE_DIR}/include")
  target_include_directories(gtest      SYSTEM BEFORE INTERFACE
    "${gtest_SOURCE_DIR}/include")
endif()

# Now simply link against gmock/gtest or gmock/gtest_main as needed. Eg
add_executable(justGTest usingGTestLib.cpp)
target_link_libraries(justGTest gtest)

add_executable(justGTestMain usingGTestMainLib.cpp)
target_link_libraries(justGTestMain gtest_main)

add_executable(justGMock usingGMockLib.cpp)
target_link_libraries(justGMock gmock)

add_executable(justGMockMain usingGMockMainLib.cpp)
target_link_libraries(justGMockMain gmock_main)

