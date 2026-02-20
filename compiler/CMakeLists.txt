cmake_minimum_required(VERSION 3.31)
project(Lyka_Compiler C CXX)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# -------------------------------------------------
# Find LLVM
# -------------------------------------------------
find_package(LLVM REQUIRED CONFIG)
message(STATUS "Found LLVM ${LLVM_PACKAGE_VERSION} on ${CMAKE_SYSTEM_NAME}")

# -------------------------------------------------
# Gather Sources FIRST
# -------------------------------------------------
file(GLOB SHARED_C_SOURCES CONFIGURE_DEPENDS
    "../shared/lexer/*.c"
    "../shared/parser/*.c"
)

file(GLOB COMPILER_CXX_SOURCES CONFIGURE_DEPENDS
    "src/*.cpp"
)

# -------------------------------------------------
# Create Executable
# -------------------------------------------------
add_executable(lykac
    ${COMPILER_CXX_SOURCES}
    ${SHARED_C_SOURCES}
)

# -------------------------------------------------
# Include Directories (Target-Based)
# -------------------------------------------------
target_include_directories(lykac PRIVATE
    ${LLVM_INCLUDE_DIRS}
    ${PROJECT_SOURCE_DIR}/../shared/include
    ${PROJECT_SOURCE_DIR}/../shared/lexer
    ${PROJECT_SOURCE_DIR}/../shared/parser
    src
)

# -------------------------------------------------
# LLVM Definitions
# -------------------------------------------------
target_compile_definitions(lykac PRIVATE ${LLVM_DEFINITIONS})

# -------------------------------------------------
# Link LLVM Components
# -------------------------------------------------
llvm_map_components_to_libnames(llvm_libs
    support
    core
    irreader
    executionengine
    target
    analysis
    native
)

target_link_libraries(lykac PRIVATE ${llvm_libs})