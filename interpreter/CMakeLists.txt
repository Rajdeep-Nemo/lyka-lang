# Build the libraries in order of dependency
add_subdirectory(common)
add_subdirectory(lexer)
add_subdirectory(parser)
add_subdirectory(evaluator)

# Create the final executable
add_executable(revenar main.c)

# Link the executable to the modules
target_link_libraries(revenar PRIVATE
    revenar_evaluator
    revenar_parser
    revenar_lexer
    revenar_common
)
