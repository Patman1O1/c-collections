# ─────────────────────────────────────────────────────────────────────────────
# Target: c_array
# Alias: collections::c_array
# Type: Static Library
# Dependencies: collections::c_array_headers
# ─────────────────────────────────────────────────────────────────────────────
# Target definition
add_library(c_array STATIC)

# Target alias
add_library(collections::c_array ALIAS c_array)

# Target sources
target_sources(c_array
    PRIVATE
        ${CMAKE_CURRENT_SOURCE_DIR}/c_array.c
)

# Target dependencies
target_link_libraries(c_array
    PUBLIC
        collections::c_array_headers
)

# Export configuration
include(GenerateExportHeader)

if(BUILD_SHARED_LIBS)
    # Export file name for a shared build
    set(EXPORT_HEADER_FILE export_shared.h)

    # Primary target properties
    set_target_properties(c_array PROPERTIES
            OUTPUT_NAME c_array
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            C_VISIBILITY_PRESET hidden
            VISIBILITY_INLINES_HIDDEN ON
    )

    # Define compile definitions
    target_compile_definitions(c_array
            PUBLIC
                $<$<NOT:$<BOOL:${BUILD_SHARED_LIBS}>>:C_ARRAY_STATIC_DEFINE>
    )
else()
    # Export file name for a static build
    set(EXPORT_HEADER_FILE export_static.h)

    # Primary target properties
    set_target_properties(c_array PROPERTIES
            OUTPUT_NAME c_array
            VERSION ${PROJECT_VERSION}
            SOVERSION ${PROJECT_VERSION_MAJOR}
            POSITION_INDEPENDENT_CODE ON
    )

    # A static build must see the STATIC_DEFINE guard from the generated export header
    target_compile_definitions(c_array
            PUBLIC
                C_ARRAY_STATIC_DEFINE
    )
endif()

# Generate the export header into the build tree
generate_export_header(c_array
        EXPORT_FILE_NAME include/c_array/${EXPORT_HEADER_FILE}
)

# Make the generated export header visible to this target and its consumers
target_include_directories(c_array
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/include>
        $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

if(NOT CMAKE_SKIP_INSTALL_RULES)
    include(GNUInstallDirs)

    # Install the library target (Conan/CMakeDeps provides the config + targets files)
    install(TARGETS c_array c_array_headers
            RUNTIME COMPONENT c_array
            LIBRARY COMPONENT c_array
            NAMELINK_COMPONENT c_array
            ARCHIVE COMPONENT c_array
            INCLUDES DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

    # Install the generated export header
    install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/c_array/${EXPORT_HEADER_FILE}
            COMPONENT c_array
            DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/c_array)
endif()
