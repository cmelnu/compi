Architecture
============

Overview
--------
The compiler is organized into modular components for tokenization, parsing, AST construction, and VHDL code generation.

Main Components
---------------
- **Tokenizer**: Scans C source code and produces tokens for keywords, identifiers, numbers, operators, and punctuation.
- **Parser**: Uses recursive descent to build an AST representing the structure of the C code.
- **AST**: Abstract Syntax Tree nodes represent functions, variables, assignments, and return statements.
- **Code Generator**: Traverses the AST to produce VHDL code, mapping C types to VHDL types and handling signal/port declarations.

Data Flow
---------
1. Input C file is tokenized.
2. Tokens are parsed into an AST.
3. AST is printed for debugging (optional).
4. VHDL code is generated from the AST.

Extensibility
-------------
- New C constructs can be supported by adding AST node types and parser logic.
- VHDL codegen can be expanded for more complex hardware descriptions.
