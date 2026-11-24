cmake_minimum_required(VERSION 3.20)
project(GRACE-Engine)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

add_subdirectory(Test)

set(COPY_COMMANDS)

if(WIN32)
    file(GLOB VENDOR_FILES "${CMAKE_SOURCE_DIR}/Vendor/Lib/Windows/x64/*.dll")
elseif(UNIX AND NOT APPLE)
    file(GLOB VENDOR_FILES "${CMAKE_SOURCE_DIR}/Vendor/Lib/Linux/x64/*.so*")
endif()

if(VENDOR_FILES)
    list(APPEND COPY_COMMANDS
        COMMAND ${CMAKE_COMMAND} -E copy_if_different
            ${VENDOR_FILES}
            ${CMAKE_BINARY_DIR}/Test/
    )
endif()

if(COPY_COMMANDS)
    add_custom_target(copy_resources ALL
        ${COPY_COMMANDS}
        COMMENT "Kopiere Vendor-Bibliotheken und Assets in den Runtime-Ordner..."
    )

    # Stellt sicher, dass copy_resources ausgeführt wird, wenn 'runtime' gebaut wird
    add_dependencies(Test copy_resources)
endif()
