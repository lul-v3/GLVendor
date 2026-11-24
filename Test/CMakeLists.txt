project(Test)

find_package(OpenGL REQUIRED)

file(GLOB_RECURSE SRC
    Source/*.h    
    Source/*.cpp
)

file(GLOB_RECURSE IMPL_SRC
    Impl/*.c
    Impl/*.cpp
)

add_executable(Test ${SRC} ${IMPL_SRC})

# EXPLIZITE INCLUDE-PFADE für compile_commands.json
target_include_directories(Test PUBLIC
    Source
    ${CMAKE_SOURCE_DIR}/Vendor/Include
    ${OPENGL_INCLUDE_DIRS}     # Wichtig für OpenGL
    ${GLFW3_INCLUDE_DIRS}       # Falls GLFW3 gefunden wurde
    # Falls OpenGL includes in /usr/include/GL liegen, füge hinzu:
    /usr/include
)


if(WIN32)
    message(STATUS "Linking framework with Windows libraries")
    target_link_directories(Test PUBLIC
        ${CMAKE_SOURCE_DIR}/Vendor/Lib/Windows/x64
    )
elseif(UNIX AND NOT APPLE)
    message(STATUS "Linking framework with Linux libraries")
    target_link_directories(Test PUBLIC
        ${CMAKE_SOURCE_DIR}/Vendor/Lib/Linux/x64
    )
else()
    message(STATUS "UNKNOWN OS: ${CMAKE_SYSTEM_NAME}")
endif()

if(WIN32)
    target_link_libraries(Test PUBLIC
        SDL3
        OpenGL::GL
    )
elseif(UNIX AND NOT APPLE)
    target_link_libraries(Test PUBLIC
        SDL3
        OpenGL::GL
    )
else()
    message(STATUS "UNKNOWN OS: ${CMAKE_SYSTEM_NAME}")
endif()

# Linux:
set_target_properties(Test PROPERTIES
    OUTPUT_NAME "Test"
)

# Debug: Zeige alle Include-Pfade
message(STATUS "OpenGL INCLUDE DIRS: ${OPENGL_INCLUDE_DIRS}")
message(STATUS "OpenGL LIBRARIES: ${OPENGL_LIBRARIES}")

# Copy Engine library to runtime folder
add_custom_command(TARGET Test POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different
        $<TARGET_FILE:Test>
        ${CMAKE_BINARY_DIR}/Test/
)
