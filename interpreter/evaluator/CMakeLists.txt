add_library(revenar_evaluator
    eval.c
    eval.h
    environment.c
    environment.h
)

# Link to parser (which transitively links lexer and common)
target_link_libraries(revenar_evaluator PUBLIC revenar_parser)

target_include_directories(revenar_evaluator PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
