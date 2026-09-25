# ── Settings ─────────────────────────────────────────────────────────────────
enable_language(CXX)
set(CMAKE_CXX_STANDARD 26)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# ── Google Test ──────────────────────────────────────────────────────────────
include(GoogleTest)
enable_testing()

# ───────────────────────────────────────────────────────────────────────────── 
# Target: c_array_test
# Alias: collections::c_array_test
# Type: Executable
# Dependencies: GTest::gtest_main,
#               GTest::gmock_main,
#               collections::c_array_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_executable(c_array_test)

# Target alias
add_executable(collections::c_array_test
    ALIAS c_array_test
)

# Target sources
target_sources(c_array_test
    PRIVATE
        ${CMAKE_SOURCE_DIR}/src/array.c
        ${CMAKE_CURRENT_SOURCE_DIR}/array_tests.cpp
)

# Target dependencies
target_link_libraries(c_array_test
    PRIVATE
        collections::c_array_headers
        GTest::gtest_main
        GTest::gmock_main
)

# Test discovery
gtest_discover_tests(c_array_test)
