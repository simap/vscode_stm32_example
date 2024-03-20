cmake_minimum_required(VERSION 3.20)

project("stvscode" C CXX ASM)

include(cmake/st-project.cmake)

add_executable(${PROJECT_NAME})
add_st_target_properties(${PROJECT_NAME})

target_link_options(${PROJECT_NAME}
  PUBLIC
    LINKER:-Map=${PROJECT_NAME}.map
)