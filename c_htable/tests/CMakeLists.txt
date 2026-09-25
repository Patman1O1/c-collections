# ── Settings ─────────────────────────────────────────────────────────────────
enable_language(CXX)
set(CMAKE_CXX_STANDARD 23)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

# ── Google Test ──────────────────────────────────────────────────────────────
include(GoogleTest)
enable_testing()

# ───────────────────────────────────────────────────────────────────────────── 
# Target: c_htable_test
# Alias: collections::c_htable_test
# Type: Executable
# Dependencies: GTest::gtest_main,
#               GTest::gmock_main,
#               collections::c_htable_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_executable(c_htable_test)

# Target alias
add_executable(collections::c_htable_test
    ALIAS c_htable_test
)

# Target sources
target_sources(c_htable_test
    PRIVATE
        ${CMAKE_SOURCE_DIR}/src/c_htable.c
        ${CMAKE_CURRENT_SOURCE_DIR}/c_htable_test.cpp
)

# Target dependencies
target_link_libraries(c_htable_test
    PRIVATE
        collections::c_htable_headers
        GTest::gtest_main
        GTest::gmock_main
)

# Test discovery
gtest_discover_tests(c_htable_test)
