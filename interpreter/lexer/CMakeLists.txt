add_library(revenar_lexer
    lexer.c
    lexer.h
)

target_link_libraries(revenar_lexer PUBLIC revenar_common)

target_include_directories(revenar_lexer PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
