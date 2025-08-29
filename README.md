# 🚀 Compi: C-to-VHDL Compiler

Compi is a tool that translates C source code into VHDL hardware descriptions.  
Easily convert your algorithms from software to hardware! 🖥️➡️🔌


## ✨ Features

- 📝 Lexical analysis of C code (tokenizer)
- 🏗️ Parsing of function declarations, parameter lists, variable declarations, assignments, return statements, and `if`/`else if`/`else` control flow
- ➕ Expression parsing with proper precedence and associativity:
    - Arithmetic: `+ - * /`
    - Shifts: `<< >>`
    - Bitwise: `& | ^`
    - Comparisons: `== != < <= > >=`
    - Logical: `&& ||` and unary `!`
    - Unary minus: `-x`
- 🧱 Abstract Syntax Tree (AST) construction with improved visualization (named nodes for binary/unary ops, if/else-if/else)
- 🛠️ Generation of VHDL entities and architecture skeletons
- 🔄 Automatic type mapping between C types (`int`, `float`, `double`, `char`) and VHDL types
- 🗂️ Example C files for testing in `examples/`
- 📚 Sphinx documentation in `docs/` with Read the Docs theme
- ✅ Array support:
   - Parse and generate VHDL for C arrays of types `int`, `float`, `double`, and `char`
   - Array declaration and initialization: `int arr[3] = {1,2,3};`, `float arr[2] = {1.0, 2.5};`, `char arr[4] = {'a','b','c','d'};`
   - Array access and assignment: `arr[i] = x;`, `y = arr[j];`
   - Returning array elements: `return arr[i];`
   - Full index expressions inside brackets: `arr[i+1]`, `arr[(j<<1)+k]`, `return arr[2*i]`
   - Type mapping:
      - `int[]` → `std_logic_vector(31 downto 0)`
      - `float[]`, `double[]` → `real`
      - `char[]` → `character`
   - Initializers are converted to valid VHDL literals for each type
   - Array element access and assignment use VHDL syntax: `arr(i)`

-- 🔁 **For loop and nested for loop support**
   - Parse and generate VHDL for C-style `for (init; cond; incr) { ... }` loops
   - Supports variable declarations and assignments in loop headers
   - Nested for loops are parsed and translated to VHDL (see example below)
   - VHDL codegen desugars for-loops into initialization, while loop, and increment logic
   - Example:

```c
int for_loop_sum(int n) {
   int sum = 0;
   for (int i = 0; i < n; i++) {
      for (int j = 0; j < n; j++) {
         sum = sum + i + j;
      }
   }
   return sum;
}
```

-- 🔁 **While loop, break, and continue support**
   - Parse and generate VHDL for `while (<expr>) { ... }` loops
   - Support for `break;` and `continue;` statements inside loops, including inside `if`, `else if`, and `else` blocks within loops
   - VHDL codegen emits `exit;` for `break` and `next;` for `continue`

## 🛠️ Installation

```bash
git clone https://github.com/cmelnu/compi.git
cd compi
mkdir build
cd build
cmake ..
make
```

## ▶️ Usage

```bash
./compi input.c output.vhdl
```

**Developer Debug Output:**

To enable verbose debug output for developers, configure the build with the `-DDEBUG=ON` argument:

```bash
cmake -DDEBUG=ON ..
make
```

This will provide additional debug prints and diagnostics during parsing and code generation.


## 📖 Documentation

Sphinx documentation is available in the `docs/` folder.  
To build the docs:
```bash
cmake --build . --target docs
"$BROWSER" docs/build/html/index.html
```

## 🗂️ Project Structure

- `src/` — Source code (.c, .h files)
- `examples/` — Example C files for testing
- `docs/` — Sphinx documentation
- `build/` — Build output
- `CMakeLists.txt` — Build configuration
- `.gitignore` — Git ignore rules

## 🚧 Known Issues

- Signal name collision for local variable 'result' in VHDL output ports (should be renamed to 'internal_result')
- Global variables not yet implemented
- Logical operators are implemented as combinational boolean expressions; C short-circuit evaluation semantics are not modeled in hardware
- VHDL codegen does not optimize for hardware resources or timing

## ✅ Supported operators and semantics

- Arithmetic: `+ - * /` are emitted as VHDL arithmetic on unsigned/signed as needed
- Shifts: `<< >>` map to `shift_left/shift_right`
- Bitwise: `& | ^` map to VHDL `and / or / xor` on `unsigned(...)`
- Comparisons: `== != < <= > >=` map to VHDL comparisons; numeric literals/identifiers are cast using `to_unsigned/to_signed/unsigned(...)` as appropriate
- Logical: `&& ||` are emitted as boolean `and/or` after booleanizing each operand as `(unsigned(expr) /= 0)` if not already boolean
- Unary: `-x` (unary minus) and `!x` (logical not). `!x` becomes `not ( ... )` if boolean; otherwise `(unsigned(expr) = 0)`
- Control flow: `while` loops, `break`, and `continue` statements are supported and mapped to VHDL loop constructs

## 🗺️ Roadmap

Here’s what’s planned next for Compi, based on the current state of `parse.c` and recent development:

### ✅ Current Capabilities
- Function declarations and parameter parsing
- Variable declarations and initializations
- Assignment statements (`x = value;`)
- Return statements with expressions
- AST construction and visualization
- Parsing and code generation for `if`, `else if`, and `else` statements
- Support for unary minus and negative literals/identifiers in expressions
- Basic VHDL code generation with type mapping
- Parsing and code generation for `while` loops, `break`, and `continue` statements
- Parsing and code generation for C-style `for` loops, including nested for loops

### 🚧 Roadmap

1. **Control Flow Statements**
   - Next: Add support for `for` loops

2. **Global Variable Support**
   - Parse and represent global variable declarations
   - Generate VHDL for global signals

3. **Function Calls**
   - Parse function calls within expressions and statements
   - Inline or generate VHDL for simple calls

4. **Error Handling & Diagnostics**
   - Improve error messages and diagnostics for unsupported constructs

5. **VHDL Codegen Enhancements**
   - Optimize generated VHDL for hardware resources and timing
   - Handle signal name collisions (e.g., local `result`)

6. **Documentation & Examples**
   - Expand Sphinx documentation with module, function, and data structure details
   - Add more example C files and expected VHDL outputs

7. **Code Cleanup and Restructuring**
   - Ensure clean code, improve maintainability, and refactor as needed