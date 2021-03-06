# Setup the testing executable
# 
# Written by Owen T. Parkins

include(CTest)
include(GoogleTest)

# Setup the test framework
include(CMake/GoogleTestSetup.cmake.in)

# Add the test exe
add_executable(${projectTestName}
	main.cpp
)

if (CMAKE_CXX_COMPILER_ID STREQUAL "Clang")
    # using Clang
    message(STATUS "Clang detected, adding compile flags")
    target_compile_options(${projectTestName} PRIVATE -Wall -Werror)
    # Enforce C++17 features
    target_compile_features(${projectTestName} PRIVATE cxx_std_17)
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")
    # using GCC
    message(STATUS "GCC detected, adding compile flags")
    target_compile_options(${projectTestName} PRIVATE -Wall -Werror)
    # Enforce C++17 features
    target_compile_features(${projectTestName} PRIVATE cxx_std_17)
elseif (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    # using Visual Studio C++
    message(STATUS "msvc detected, adding compile flags")
    target_compile_options(${projectTestName} PRIVATE  /std:c++latest /EHsc)
endif()

# Need this to help find test files
target_compile_definitions(${projectTestName} PUBLIC
		TEST_DIRECTORY="${PROJECT_SOURCE_DIR}/test"
)

# Link to the KiLib library
target_link_libraries(${projectTestName} gtest_main KiLib)

target_include_directories(${projectTestName}
PUBLIC
		${CMAKE_INSTALL_PREFIX}/${CMAKE_INSTALL_INCLUDEDIR}
PRIVATE
		./
)

# Discover tests
gtest_discover_tests(${projectTestName})
add_test(NAME KiLibTest COMMAND ${projectTestName})