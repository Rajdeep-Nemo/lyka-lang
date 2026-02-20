# Lyka Language Developer Guide

All terminal commands provided below are intended for the **Bash** shell.  
Users on Windows should use WSL (Windows Subsystem for Linux) or Git Bash.

---

## 1. Project Architecture

The Lyka interpreter follows a classic modular compiler architecture aimed at maintainability and strict separation of concerns.  
The codebase is written in C11 and relies on CMake for build orchestration.

### Directory Breakdown

The project is organized into discrete modules, each handling a specific stage of the execution pipeline:

```text
lyka-lang/
├── shared/                       # THE FRONTEND
│   ├── include/                  # Global Header Files
│   │   ├── token.h               # Token definitions & Type enums
│   │   ├── ast.h                 # LLVM-ready tree nodes (Expr & Stmt)
│   │   ├── types.h               # Lyka type system (int, float, etc.)
│   │   └── common.h              # Macros, memory management, & error types
│   ├── lexer/                    # Lexical Analysis
│   │   ├── lexer.c               # Character-to-token logic
│   │   └── lexer.h               # Scanner interface
│   └── parser/                   # Syntax Analysis
│       ├── parser_shared.h       # ParserState struct & utility
│       ├── parser_utils.c        # peek(), advance(), match(), consume()
│       ├── expression.c          # Precedence-based math
│       └── statement.c           # if, while, let, blocks
├── interpreter/                  # BACKEND A: Tree-Walking Interpreter
│   ├── src/
│   │   ├── main.c                # Interpreter entry point
│   │   ├── evaluator.c           # AST recursive visitor
│   │   ├── environment.c         # Runtime symbol table (scopes)
│   │   └── value.h               # Runtime representation of Raven data
│   └── CMakeLists.txt            # Builds 'lyka' (Links shared/)
├── compiler/                     # BACKEND B: LLVM Compiler
    ├── src/
    │   ├── main.cpp              # Compiler entry point (C++)
    │   ├── codegen.cpp           # AST to LLVM IR conversion
    │   ├── codegen.hpp           # Header for IR generation
    │   └── llvm_utils.cpp        # LLVM context and optimization passes
    └── CMakeLists.txt            # Builds 'lykac' (Finds LLVM, links shared/)
```

---

## 2. Prerequisites

Before building, ensure the development environment has the necessary toolchain installed.

### Required Tools

#### Interpreter:
- **C Compiler**: GCC or Clang (supporting C11 standard)
- **Build System**: CMake 3.31+  
- **Version Control**: Git

#### Compiler:
- **C Compiler**: GCC or Clang (supporting C11 standard)
- **C++ Compiler**: G++ or Clang++ (supporting C++17 standard)
- **Build System**: CMake 3.31+  
- **Build Generator**: Ninja (recommended) or GNU Make
- **LLVM**: LLVM 17+ (development package with headers and CMake config)
- **System Libraries (if required by LLVM build)**: zlib, zstd, curl, libedit
- **Version Control**: Git

### Install Command (Debian/Ubuntu)

```bash
sudo apt update && sudo apt install -y \
  build-essential \
  clang \
  cmake \
  ninja-build \
  git \
  llvm-19 \
  llvm-19-dev \
  zlib1g-dev \
  libzstd-dev \
  libcurl4-openssl-dev \
  libedit-dev
```

---

## 3. Build Instructions

An "out-of-source" build workflow is used to keep the source directories clean.

### A. Standard Build (For Users)

This compiles the `lyka` executable optimized for release. It skips the testing suite to speed up compilation.

1. **Initialize the Build Directory**

```bash
mkdir build
cd build
```

2. **Configure the Project**

```bash
cmake -DCMAKE_BUILD_TYPE=Release ..
```

3. **Compile**
```bash
cmake --build .
```

4. **Run the Interpreter**

```bash
./interpreter/lyka
```

### B. Developer Build (For Contributors)

This configuration enables Unit Tests, Debug Symbols, and stricter compiler warnings.  
Use this mode when adding features or fixing bugs.

1. **Configure with Debugging and Tests Enabled**

```bash
cd build
cmake -DCMAKE_BUILD_TYPE=Debug -DLYKA_BUILD_TESTS=ON ..
```

2. **Compile**
```bash
cmake --build .
```

3. **Run the Test Suite**

We use ctest (bundled with CMake) to validate the build.

```bash
ctest --output-on-failure
```

---

## 4. Module Dependency Graph

Understanding how the libraries link is crucial for avoiding circular dependencies:

- **Common**: No dependencies. Base definitions.  
- **Lexer**: Depends on `Common`.  
- **Parser**: Depends on `Lexer` and `Common`.  
- **Evaluator**: Depends on `Parser` (and transitively `Lexer`).  
- **Main**: Links all modules to produce the binary.

---

## 5. Adding a New Feature

When adding a new language feature (e.g., a "While Loop"):

1. **Common**: Add `TOKEN_WHILE` to `common/token.h`.  
2. **Lexer**: Update `lexer/lexer.c` to recognize the string `"while"`.  
3. **Parser**: Update `parser/ast.h` to define a `WhileNode` struct and `parser/parser.c` to parse the grammar.  
4. **Evaluator**: Update `evaluator/eval.c` to execute the loop logic.  
5. **Test**: Create a new test case in `tests/` to verify the behavior.
