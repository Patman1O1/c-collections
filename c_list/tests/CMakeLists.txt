# ── Settings ─────────────────────────────────────────────────────────────────
enable_language(CXX)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# ── Google Test ──────────────────────────────────────────────────────────────
include(GoogleTest)
enable_testing()

# ───────────────────────────────────────────────────────────────────────────── 
# Target: c_list_test
# Alias: collections::c_list_test
# Type: Executable
# Dependencies: GTest::gtest_main,
#               GTest::gmock_main,
#               collections::c_list_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_executable(c_list_test)

# Target alias
add_executable(collections::c_list_test
    ALIAS c_list_test
)

# Target sources
target_sources(c_list_test
    PRIVATE
        ${CMAKE_SOURCE_DIR}/src/c_list.c
        ${CMAKE_CURRENT_SOURCE_DIR}/c_list_test.cpp
)

# Target dependencies
target_link_libraries(c_list_test
    PRIVATE
        collections::c_list_headers
        GTest::gtest_main
        GTest::gmock_main
)

# Test discovery
gtest_discover_tests(c_list_test)
