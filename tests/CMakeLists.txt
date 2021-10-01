project(Optimization-Tests CXX)

# Find Eigen library
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_CURRENT_SOURCE_DIR}/../cmake/")
find_package(Eigen3 3.3.3 REQUIRED)
if(EIGEN3_FOUND)
message(STATUS "Found Eigen3 library (version ${EIGEN3_VERSION})")
message(STATUS "Eigen3 include directory: ${EIGEN3_INCLUDE_DIR}\n")
else()
message(STATUS "Eigen library not found!")
endif()

# Add the Eigen include directories
include_directories(${EIGEN3_INCLUDE_DIR})

# Find additional libraries required by NITRO
find_package(Umfpack REQUIRED)
find_package(BLAS REQUIRED)

# Add the Eigen and UMFPack include directories
include_directories(${EIGEN3_INCLUDE_DIR} ${UMFPACK_INCLUDES})

# Get GoogleTest
find_package(GTest QUIET)
if ((NOT ${gtest_FOUND}) OR (NOT TARGET GTest::gtest))
include(${CMAKE_CURRENT_SOURCE_DIR}/../cmake/googletest.cmake)
fetch_googletest(
    ${CMAKE_CURRENT_SOURCE_DIR}/../cmake
    ${PROJECT_BINARY_DIR}/googletest
    )
endif()
set(GTest_LIBRARIES GTest::gtest GTest::gtest_main)

# Add GTest unit tests
enable_testing()

# UNIT TESTS
set(TEST_SRCS
        ${CMAKE_CURRENT_SOURCE_DIR}/IterativeSolvers_unit_test.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/TNT_unit_test.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/TNLS_unit_test.cpp
        ${CMAKE_CURRENT_SOURCE_DIR}/GradientDescent_unit_test.cpp
)

# INDIVIDUAL UNIT TESTS

add_executable(IterativeSolvers_unit_test ${CMAKE_CURRENT_SOURCE_DIR}/IterativeSolvers_unit_test.cpp)
target_link_libraries(IterativeSolvers_unit_test Optimization ${UMFPACK_LIBRARIES} ${GTest_LIBRARIES})
add_test(NAME IterativeSolvers_unit_test COMMAND IterativeSolvers_unit_test)

add_executable(TNT_unit_test ${CMAKE_CURRENT_SOURCE_DIR}/TNT_unit_test.cpp)
target_link_libraries(TNT_unit_test Optimization ${GTest_LIBRARIES})
add_test(NAME TNT_unit_test COMMAND TNT_unit_test)

add_executable(TNLS_unit_test ${CMAKE_CURRENT_SOURCE_DIR}/TNLS_unit_test.cpp)
target_link_libraries(TNLS_unit_test Optimization ${GTest_LIBRARIES})
add_test(NAME TNLS_unit_test COMMAND TNLS_unit_test)

add_executable(GradientDescent_unit_test ${CMAKE_CURRENT_SOURCE_DIR}/GradientDescent_unit_test.cpp)
target_link_libraries(GradientDescent_unit_test Optimization ${GTest_LIBRARIES})
add_test(NAME GradientDescent_unit_test COMMAND GradientDescent_unit_test)

# RUN ALL UNIT TESTS
add_executable(RunAllUnitTests ${TEST_SRCS})
target_link_libraries(RunAllUnitTests Optimization ${UMFPACK_LIBRARIES} ${GTest_LIBRARIES})
#add_test(NAME RunAllUnitTests COMMAND RunAllUnitTests)