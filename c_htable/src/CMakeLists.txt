# ─────────────────────────────────────────────────────────────────────────────
# Target: c_htable
# Alias: collections::c_htable
# Type: Static Library
# Dependencies: collections::c_htable_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_library(c_htable STATIC)

# Target alias
add_library(collections::c_htable ALIAS c_htable)

# Target sources
target_sources(c_htable
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/c_htable.c
)

# Target dependencies
target_link_libraries(c_htable
    PUBLIC
        collections::c_htable_headers
)

# Export configuration
include(GenerateExportHeader)

if(BUILD_SHARED_LIBS)
    # Export file name for a shared build
    set(EXPORT_HEADER_FILE export_shared.h)

    # Primary target properties
    set_target_properties(c_htable PROPERTIES
            OUTPUT_NAME c_htable
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            C_VISIBILITY_PRESET hidden
            VISIBILITY_INLINES_HIDDEN ON
    )

    # Define compile definitions
    target_compile_definitions(c_htable
            PUBLIC
                $<$<NOT:$<BOOL:${BUILD_SHARED_LIBS}>>:C_HTABLE_STATIC_DEFINE>
    )
else()
    # Export file name for a static build
    set(EXPORT_HEADER_FILE export_static.h)

    # Primary target properties
    set_target_properties(c_htable PROPERTIES
            OUTPUT_NAME c_htable
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            POSITION_INDEPENDENT_CODE ON
    )

    # A static build must see the STATIC_DEFINE guard from the generated export header
    target_compile_definitions(c_htable
            PUBLIC
                C_HTABLE_STATIC_DEFINE
    )
endif()

# Generate the export header into the build tree
generate_export_header(c_htable
        EXPORT_FILE_NAME include/c_htable/${EXPORT_HEADER_FILE}
)

# Make the generated export header visible to this target and its consumers
target_include_directories(c_htable
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

if(NOT CMAKE_SKIP_INSTALL_RULES)
    include(GNUInstallDirs)

    # Install the library target (Conan/CMakeDeps provides the config + targets files)
    install(TARGETS c_htable c_htable_headers
            RUNTIME COMPONENT c_htable
            LIBRARY COMPONENT c_htable
            NAMELINK_COMPONENT c_htable
            ARCHIVE COMPONENT c_htable
            INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

    # Install the generated export header
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/c_htable/${EXPORT_HEADER_FILE}
            COMPONENT c_htable
            DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/c_htable)
endif()
