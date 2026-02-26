cmake_minimum_required(VERSION 3.20)

project(OpenGLTutorial)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# GLFW options
set(GLFW_BUILD_DOCS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(GLFW_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)

add_subdirectory(libs/glfw)

# Assimp options
set(ASSIMP_BUILD_TESTS OFF CACHE BOOL "" FORCE)
set(ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL "" FORCE)
set(ASSIMP_INSTALL OFF CACHE BOOL "" FORCE)

add_subdirectory(libs/assimp)

# GLAD
add_library(glad
    libs/glad/src/glad.c
)

target_include_directories(glad PUBLIC
    libs/glad/include
)

# Source files
file(GLOB_RECURSE SRC_FILES
    src/*.cpp
)

add_executable(OpenGLTutorial ${SRC_FILES})

# Include project headers
target_include_directories(OpenGLTutorial PRIVATE
    src
    libs
)

# Link libraries
target_link_libraries(OpenGLTutorial
    glfw
    glad
    assimp
)

# Platform OpenGL
if (WIN32)
    target_link_libraries(OpenGLTutorial opengl32)
elseif(UNIX AND NOT APPLE)
    target_link_libraries(OpenGLTutorial GL)
elseif(APPLE)
    find_library(OpenGL_LIBRARY OpenGL)
    target_link_libraries(OpenGLTutorial ${OpenGL_LIBRARY})
endif()

# Copy assets to build folder
file(COPY ${CMAKE_SOURCE_DIR}/assets
     DESTINATION ${CMAKE_BINARY_DIR})

add_custom_command(TARGET OpenGLTutorial POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_directory
    ${CMAKE_SOURCE_DIR}/assets
    $<TARGET_FILE_DIR:OpenGLTutorial>/assets
)