# BLD - Build Automation Tool

A modern, Python-based reimplementation of the classic `make` utility, designed to provide efficient build automation through dependency graph analysis and incremental compilation.

## Overview

BLD is a sophisticated build automation tool that uses `Bldfile` configuration files to manage complex build processes. Built with a modular architecture featuring separate parsing, dependency resolution, and execution engines, BLD offers advanced features like incremental builds, parallel execution, and comprehensive error handling while maintaining the familiar make-like syntax.

## Features

- **Smart Incremental Builds**: Timestamp-based dependency checking for optimal build performance
- **Robust Dependency Resolution**: Advanced DAG algorithms with cycle detection and validation
- **Variable System**: Full variable substitution with nested expansion support
- **Parallel Execution**: Multi-threaded build process for faster compilation
- **Comprehensive Error Handling**: Detailed error reporting and graceful failure recovery
- **Flexible Configuration**: Intuitive `Bldfile` syntax with advanced features
- **Cross-Platform Support**: Works on Unix-like systems and Windows

## Installation & Setup

### Prerequisites
- Python 3.8 or higher
- pip package manager

### Installation
```bash
# Clone the repository
git clone https://github.com/yourusername/bld.git
cd bld

# Install dependencies
pip install -r requirements.txt

# Install in development mode
pip install -e .

# Or install system-wide
python setup.py install

# Make executable available globally
chmod +x bld
sudo ln -s $(pwd)/bld /usr/local/bin/bld
```

### Development Setup
```bash
# Install development dependencies
pip install -r requirements-dev.txt

# Run tests
python -m pytest tests/

# Run linting
flake8 src/
black src/

# Generate documentation
sphinx-build docs/ docs/_build/
```

## Usage

```bash
bld [command]
```

### Available Commands

| Command | Description |
|---------|-------------|
| `all` (default) | Build the entire project |
| `clean` | Remove intermediate build files |
| `re` | Rebuild (clean + all) |
| `fclean` | Full clean (remove all generated files) |

### Advanced Usage

```bash
# Build with parallel execution (4 jobs)
bld -j4

# Build specific target with dependencies
bld target_name

# Dry run (show what would be built)
bld --dry-run

# Verbose output with timing
bld --verbose --time

# Force rebuild (ignore timestamps)
bld --force

# Build with custom Bldfile
bld -f custom.bld

# Show dependency graph
bld --show-deps target_name

# Debug mode with detailed logging
bld --debug clean all
```

## Performance Features

### Incremental Builds
BLD automatically determines which targets need rebuilding based on:
- File modification timestamps
- Dependency chain analysis
- Command line argument changes
- Environment variable modifications

### Parallel Execution
```bash
# Automatic parallelism detection
bld -j auto

# Manual job control
bld -j8  # Use 8 parallel jobs

# Fine-grained control
bld --max-load 4.0  # Respect system load
```

### Build Caching
```bash
# Enable build cache
export BLD_CACHE_DIR=~/.bld_cache
bld --cache

# Clear cache
bld --clear-cache
```

## Advanced Bldfile Syntax

### Basic Structure
```makefile
# Variables with different scoping
GLOBAL_VAR = value
export ENV_VAR = exported_value

# Target with dependencies and commands
target: dependency1 dependency2
	@echo "Building target..."
	$(CC) $(CFLAGS) -o $@ $^

# Pattern rules
%.o: %.c
	$(CC) $(CFLAGS) -c $< -o $@
```

### Advanced Features
```makefile
# Conditional variables
ifeq ($(DEBUG), 1)
    CFLAGS += -g -DDEBUG
else
    CFLAGS += -O2 -DNDEBUG
endif

# Functions and built-ins
SOURCES = $(wildcard src/*.c)
OBJECTS = $(SOURCES:.c=.o)
DEPS = $(OBJECTS:.o=.d)

# Multi-line commands with proper escaping
complex_target:
	@if [ ! -d build ]; then \
		mkdir -p build; \
	fi
	$(CC) $(CFLAGS) $(SOURCES) -o build/$(NAME)
```

## Project Architecture

```
bld/
├── src/
│   ├── parser/
│   │   ├── __init__.py
│   │   ├── lexer.py           # Tokenize Bldfile syntax
│   │   ├── parser.py          # Parse tokens into Abstract Syntax Tree
│   │   └── validator.py       # Validate syntax and semantic rules
│   ├── core/
│   │   ├── __init__.py
│   │   ├── target.py          # Target class with dependency tracking
│   │   ├── variable.py        # Variable expansion and scoping
│   │   ├── dag.py             # DAG construction and algorithms
│   │   └── exceptions.py      # Custom exception classes
│   ├── builder/
│   │   ├── __init__.py
│   │   ├── executor.py        # Command execution engine
│   │   ├── dependency_resolver.py  # Topological sorting algorithms
│   │   ├── incremental.py     # Timestamp-based build logic
│   │   └── parallel.py        # Multi-threaded execution
│   ├── cli/
│   │   ├── __init__.py
│   │   └── runner.py          # Command-line interface
│   └── utils/
│       ├── __init__.py
│       ├── file_utils.py      # File system operations
│       └── logging.py         # Build process logging
├── tests/
│   ├── unit/
│   ├── integration/
│   └── fixtures/
├── examples/
│   ├── simple_c_project/
│   ├── complex_cpp_project/
│   └── multi_language_project/
├── docs/
├── requirements.txt
├── setup.py
├── bld                        # Main executable script
└── README.md
```

### Component Details

#### Parser Module (`src/parser/`)

- **`lexer.py`**: Tokenizes Bldfile content, handling comments, variables, targets, and commands with proper error location tracking
- **`parser.py`**: Builds an Abstract Syntax Tree (AST) from tokens, implementing a recursive descent parser for Bldfile grammar
- **`validator.py`**: Validates AST for semantic correctness, checks for undefined variables, circular dependencies, and syntax violations

#### Core Module (`src/core/`)

- **`target.py`**: Defines Target class with dependency relationships, command lists, and timestamp tracking for incremental builds
- **`variable.py`**: Implements variable expansion engine with support for nested variables, conditional expansion, and scoping rules
- **`dag.py`**: Constructs and manipulates Directed Acyclic Graphs, implements topological sorting and cycle detection algorithms
- **`exceptions.py`**: Custom exception hierarchy for different error types (parsing, dependency, execution errors)

#### Builder Module (`src/builder/`)

- **`executor.py`**: Executes build commands with proper environment handling, output capture, and error reporting
- **`dependency_resolver.py`**: Implements advanced dependency resolution algorithms including Kahn's algorithm for topological sorting
- **`incremental.py`**: Manages timestamp-based incremental builds, file modification tracking, and rebuild necessity determination
- **`parallel.py`**: Provides multi-threaded execution capabilities with proper synchronization and resource management

#### CLI Module (`src/cli/`)

- **`runner.py`**: Command-line interface with argument parsing, help system, and integration with all core components

## Core Algorithms & Data Structures

### Variable Expansion Engine
```python
class VariableExpander:
    """
    Handles nested variable expansion with cycle detection
    Example: $(CC_$(ARCH)) → $(CC_x86_64) → gcc
    """
    def expand(self, expression, context, visited=None):
        # Recursive expansion with circular reference detection
        # Supports conditional expansion and built-in functions
        pass
```

### Dependency Graph (DAG)
```python
class DependencyGraph:
    """
    Directed Acyclic Graph for build dependency management
    
    Structure:
    - Nodes: Build targets with metadata
    - Edges: Dependency relationships
    - Algorithms: Topological sort, cycle detection, incremental analysis
    """
    def __init__(self):
        self.nodes = {}  # target_name -> TargetNode
        self.edges = {}  # target_name -> [dependencies]
        
    def add_target(self, target):
        # Add target to graph with dependency validation
        pass
        
    def topological_sort(self, start_target="all"):
        # Kahn's algorithm for build order determination
        # Returns ordered list of targets to build
        pass
        
    def detect_cycles(self):
        # DFS-based cycle detection with path reporting
        pass
```

### Incremental Build Engine
```python
class IncrementalBuilder:
    """
    Timestamp-based incremental build logic
    
    Determines rebuild necessity based on:
    - File modification times
    - Dependency freshness
    - Command line changes
    - Environment variable changes
    """
    def needs_rebuild(self, target):
        # Multi-factor rebuild necessity analysis
        return (
            self._check_timestamps(target) or
            self._check_dependencies(target) or
            self._check_command_changes(target)
        )
```

### Hash Map (Variable Storage)
Variables are stored with hierarchical scoping:
```
Global Scope: {
    "CC": "gcc",
    "CFLAGS": "-Wall -O2",
    "NAME": "myprogram"
}

Target Scope: {
    "main.o": {
        "CFLAGS": "-Wall -O2 -g"  # Target-specific override
    }
}
```

### Example DAG Representation
```
Bldfile:
NAME = myprogram
$(NAME): main.o utils.o
main.o: main.c utils.h
utils.o: utils.c utils.h

Generated DAG:
[myprogram] ──→ [main.o] ──→ [main.c]
      │              └──→ [utils.h]
      └─────→ [utils.o] ──→ [utils.c]
                     └──→ [utils.h]

Build Order (Topological Sort):
1. main.c, utils.c, utils.h (source files)
2. main.o, utils.o (object files)
3. myprogram (final executable)
```

## Documentation

For more information about build automation concepts, visit the [GNU Make Documentation](https://www.gnu.org/software/make/manual/make.html).

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

[Add your license information here]

## Support

[Add contact information or issue reporting guidelines here]