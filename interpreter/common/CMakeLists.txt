# Create a library for common utilities
add_library(revenar_common
    mem_util.c
    token.h
    types.h
    mem_util.h
)

target_include_directories(revenar_common PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
