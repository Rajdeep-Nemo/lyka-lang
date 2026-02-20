cmake_minimum_required(VERSION 3.31)
project(lyka-lang)

# Set common warning levels for different compilers
if(MSVC)
    add_compile_options(/W4 /D_CRT_SECURE_NO_WARNINGS)
else()
    add_compile_options(-Wall -Wextra -Wpedantic)
endif()

add_subdirectory(interpreter)
add_subdirectory(compiler)
