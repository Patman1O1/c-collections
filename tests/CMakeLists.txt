# ── Settings ─────────────────────────────────────────────────────────────────
enable_language(CXX)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# ── Google Test ──────────────────────────────────────────────────────────────
include(GoogleTest)
enable_testing()

# ───────────────────────────────────────────────────────────────────────────── 
# Target: c_collections_test
# Alias: c_collections::c_collections_test
# Type: Executable
# Dependencies: GTest::gtest_main,
#               GTest::gmock_main,
#               c_collections::c_collections_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_executable(c_collections_test)

# Target alias
add_executable(c_collections::c_collections_test
    ALIAS c_collections_test
)

# Target sources
target_sources(c_collections_test
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/c_collections_test.cpp
)

# Target dependencies
target_link_libraries(c_collections_test
    PRIVATE
        c_collections::c_collections_headers
        GTest::gtest_main
        GTest::gmock_main
)

# Test discovery
gtest_discover_tests(c_collections_test)
