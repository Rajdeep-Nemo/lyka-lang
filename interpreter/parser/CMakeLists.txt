add_library(revenar_parser
    parser.c
    parser.h
    ast.c
    ast.h
)

# Link to lexer (which transitively links common)
target_link_libraries(revenar_parser PUBLIC revenar_lexer)

target_include_directories(revenar_parser PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
