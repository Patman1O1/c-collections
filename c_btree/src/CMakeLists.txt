# ─────────────────────────────────────────────────────────────────────────────
# Target: c_btree
# Alias: collections::c_btree
# Type: Static Library
# Dependencies: collections::c_btree_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_library(c_btree STATIC)

# Target alias
add_library(collections::c_btree ALIAS c_btree)

# Target sources
target_sources(c_btree
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/c_btree.c
)

# Target dependencies
target_link_libraries(c_btree
    PUBLIC
        collections::c_btree_headers
)

# Export configuration
include(GenerateExportHeader)

if(BUILD_SHARED_LIBS)
    # Export file name for a shared build
    set(EXPORT_HEADER_FILE export_shared.h)

    # Primary target properties
    set_target_properties(c_btree PROPERTIES
            OUTPUT_NAME c_btree
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            C_VISIBILITY_PRESET hidden
            VISIBILITY_INLINES_HIDDEN ON
    )

    # Define compile definitions
    target_compile_definitions(c_btree
            PUBLIC
                $<$<NOT:$<BOOL:${BUILD_SHARED_LIBS}>>:C_BTREE_STATIC_DEFINE>
    )
else()
    # Export file name for a static build
    set(EXPORT_HEADER_FILE export_static.h)

    # Primary target properties
    set_target_properties(c_btree PROPERTIES
            OUTPUT_NAME c_btree
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            POSITION_INDEPENDENT_CODE ON
    )

    # A static build must see the STATIC_DEFINE guard from the generated export header
    target_compile_definitions(c_btree
            PUBLIC
                C_BTREE_STATIC_DEFINE
    )
endif()

# Generate the export header into the build tree
generate_export_header(c_btree
        EXPORT_FILE_NAME include/c_btree/${EXPORT_HEADER_FILE}
)

# Make the generated export header visible to this target and its consumers
target_include_directories(c_btree
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

if(NOT CMAKE_SKIP_INSTALL_RULES)
    include(GNUInstallDirs)

    # Install the library target (Conan/CMakeDeps provides the config + targets files)
    install(TARGETS c_btree c_btree_headers
            RUNTIME COMPONENT c_btree
            LIBRARY COMPONENT c_btree
            NAMELINK_COMPONENT c_btree
            ARCHIVE COMPONENT c_btree
            INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

    # Install the generated export header
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/c_btree/${EXPORT_HEADER_FILE}
            COMPONENT c_btree
            DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/c_btree)
endif()
