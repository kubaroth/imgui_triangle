cmake_minimum_required(VERSION 3.15)
project(dear-imgui-conan CXX)

include(FetchContent)

set(CMAKE_EXPORT_COMPILE_COMMANDS 1)

message(STATUS "Downloading GLFW")
FetchContent_Declare(glfw
  GIT_REPOSITORY https://github.com/glfw/glfw
  GIT_TAG        3.3.8
  )
FetchContent_MakeAvailable(glfw)

message(STATUS "Downloading ImGui")
FetchContent_Populate(imgui
  URL https://github.com/ocornut/imgui/archive/docking.zip
  SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/imgui
  )

FetchContent_Declare(
  GLEW
  URL            https://sourceforge.net/projects/glew/files/glew/2.1.0/glew-2.1.0.zip
  SOURCE_DIR     ${CMAKE_SOURCE_DIR}/glew
  )

message(STATUS "Downloading GLEW")
FetchContent_GetProperties(glew)
if(NOT glew_POPULATED)
  FetchContent_Populate(glew)
  add_subdirectory(${CMAKE_SOURCE_DIR}/glew/build/cmake)
endif()

# find_package(glfw3 REQUIRED)
find_package(OpenGL REQUIRED)

add_executable( dear-imgui-glew
                main.cpp
                opengl_shader.cpp
                file_manager.cpp
                opengl_shader.h
                file_manager.h
                imgui/imgui.cpp
                imgui/imgui_tables.cpp
                imgui/imgui_widgets.cpp
                imgui/imgui_draw.cpp
                imgui/backends/imgui_impl_glfw.cpp
                imgui/backends/imgui_impl_glfw.h
                imgui/backends/imgui_impl_opengl3.cpp
                imgui/backends/imgui_impl_opengl3.h
                imgui/backends/imgui_impl_opengl3_loader.h
                assets/simple-shader.vs
                assets/simple-shader.fs )

target_include_directories(dear-imgui-glew
PUBLIC
  ${CMAKE_CURRENT_LIST_DIR}/imgui
  ${CMAKE_CURRENT_LIST_DIR}/imgui/backends
  ${CMAKE_CURRENT_LIST_DIR}/glew/include
)

              
add_custom_command(TARGET dear-imgui-glew
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_SOURCE_DIR}/assets/simple-shader.vs ${PROJECT_BINARY_DIR}
    COMMAND ${CMAKE_COMMAND} -E copy ${PROJECT_SOURCE_DIR}/assets/simple-shader.fs ${PROJECT_BINARY_DIR}
)

target_compile_definitions(dear-imgui-glew PUBLIC IMGUI_IMPL_OPENGL_LOADER_GLEW)
target_link_libraries(dear-imgui-glew GLEW glfw OpenGL::GL)
