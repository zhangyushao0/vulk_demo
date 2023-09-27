cmake_minimum_required(VERSION 3.15)
project(vk_demo1)
add_executable(vk_demo1 main.cpp)

find_package(Vulkan REQUIRED)
target_link_libraries(vk_demo1 PRIVATE Vulkan::Vulkan)

add_subdirectory(glm)
target_link_libraries(vk_demo1 PRIVATE glm)

add_subdirectory(glfw)
target_link_libraries(vk_demo1 PRIVATE glfw)
include_directories(glfw/include)