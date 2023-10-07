cmake_minimum_required(VERSION 3.15)
project(vk_demo1)
set(CMAKE_CXX_STANDARD 20)
add_executable(vk_demo1 main.cpp)

find_package(Vulkan REQUIRED)
target_link_libraries(vk_demo1 PRIVATE Vulkan::Vulkan)

add_subdirectory(glm)
target_link_libraries(vk_demo1 PRIVATE glm)

add_subdirectory(glfw)
target_link_libraries(vk_demo1 PRIVATE glfw)
include_directories(glfw/include)

set(GLSL_COMPILER "glslc")

set(SHADER_FILES shaders/shader.vert shaders/shader.frag)

# 对每个着色器文件运行glslc
foreach(SHADER ${SHADER_FILES})
  # 获取着色器文件的路径和文件名，不带扩展名
  get_filename_component(FILE_PATH ${SHADER} PATH)
  get_filename_component(FILE_NAME ${SHADER} EXT)
  string(REPLACE "." "" FILE_NAME ${FILE_NAME})

  # 设定输出文件名
  set(OUTPUT_FILE_SHADER "shaders/${FILE_NAME}.spv")

  # 添加自定义命令来运行glslc
  add_custom_command(
    OUTPUT ${OUTPUT_FILE_SHADER}
    COMMAND ${GLSL_COMPILER} ${CMAKE_SOURCE_DIR}/${SHADER} -o ${OUTPUT_FILE_SHADER}
    DEPENDS ${CMAKE_SOURCE_DIR}/${SHADER}
    COMMENT "Compiling ${SHADER}"
  )

  # 将输出文件添加到着色器输出列表
  list(APPEND SHADER_OUTPUTS ${OUTPUT_FILE_SHADER})
endforeach()

# 将自定义目标添加到项目中，以便在编译时运行自定义命令
add_custom_target(
  compile_shaders
  DEPENDS ${SHADER_OUTPUTS}
)

# 添加依赖，确保在编译项目之前先编译着色器
add_dependencies(vk_demo1 compile_shaders)