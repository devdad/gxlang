# GX
A fast, statically-typed language that transpiles to C. Built for systems programming, graphics, and games.

struct Player {
    name: str
    health: i32
    pos: vec3
}

fn main() {
    var p = Player{"Alice", 100, vec3{0.0, 1.0, 0.0}};
    p.health -= 10;
    print("Player {p.name} at ({p.pos.x}, {p.pos.y}, {p.pos.z})");
}
Why GX?
C performance, modern syntax. No garbage collector, no runtime. GX compiles to plain C, then to native code.
Built-in graphics types. Native vec2/3/4 and mat2/3/4 with operator overloading and swizzle (v.xy, v.zyx).
First-class C interop. Use any C library with @c_include and extern declarations. No bindings generator needed.
Compile-time system. #if, #for, #fn, #type — conditional compilation, loop unrolling, compile-time functions, and monomorphized templates. All resolved before the C compiler sees the code.
Self-contained builds. @link("opengl32") in your source file — no CMake, no Makefile.
Compile-time reflection. @fields(T), @field(T, i), @members(E) — iterate over struct fields and enum members at compile time.
Quick Start
Build the compiler
# macOS / Linux
cmake -B build && cmake --build build

# Windows (Visual Studio)
cmake -S . -B build -G "Visual Studio 17 2022"
cmake --build build --config Release
Write your first program
// hello.gx
fn main() {
    print("Hello from GX!");
}
Compile and run
# macOS / Linux
./build/GX hello.gx -o hello && ./hello

# Windows
build\Release\GX.exe hello.gx -o hello.exe
hello.exe
Features at a Glance
Types and structs
var x: i32 = 42
var name: str = "GX"
var v: vec3 = vec3{1.0, 2.0, 3.0}

struct Rect { x:f32  y:f32  w:f32  h:f32 }
enum Color { Red  Green  Blue }
union Value { i:i32  f:f32 }
String interpolation
var score: i32 = 100;
print("Score: {score} | Name: {name}");
Control flow
for (i=0:10) { print("{i}"); }

for (var item in items) {
    print(item.name);
}

match (key) {
    1, 2, 3: print("low")
    4..7:    print("mid")
    default: print("high")
}
C interop
@c_include("math.h")
extern fn sinf(x:f32):f32
extern fn cosf(x:f32):f32

fn main() {
    var angle:f32 = 3.14159;
    print("sin = {sinf(angle).str()}");
}
Compile-time templates
#type T
struct Pair {
    first: T
    second: T
}

fn main() {
    var pi: Pair<i32>;
    pi.first = 10;
    pi.second = 20;
}
Compile-time reflection
struct Point { x:f32  y:f32  z:f32 }

fn main() {
    print("Point has {@fields(Point)} fields:");
    #for (i=0:@fields(Point)) {
        print("  {@field(Point, i)}: {@field_type(Point, i)}");
    }
}
Self-contained builds
@link("opengl32")
@link("gdi32")

#if (@os == "linux") {
    @ldflags("-lX11 -lGL")
}

@c_include("sokol_app.h")
Documentation
Doc	Description
Overview	Design philosophy and architecture
Getting Started	Installation and first program
Language Tour	Walk through all language features
Core Language	Complete type system and syntax reference
C Interop	Using C libraries and extern declarations
Memory Model	Value types, pointers, allocators
Math Module	Vector, matrix, and scalar math
Compile-Time	#if, #for, #fn, #type, reflection, build directives
CLI Reference
gx [options] <input.gx>...
gx run <input.gx>           Compile and run immediately
Flag	Description
-o <file>	Output file name
-S / --emit-c	Emit C source and stop
-O1 / -O2 / -O3	Optimization level (auto-selects clang/gcc)
-I <dir>	Add module/include path
-l <name>	Link library
--cc <path>	Override C compiler
--verbose	Show compiler commands
--time	Print compilation time breakdown
-g	Debug mode (@debug becomes true)
Project Structure
src/
  frontend/           Lexer, Parser, Resolver, Type Checker
  backend/c/          C transpiler and compiler invocation
  driver/             CLI
include/gx/          Compiler headers
examples/             Example programs (01-36)
  sokol/              Graphics demos (triangle, snake, shapes)
tests/                Annotation-driven test suite
modules/              Standard modules
  math/gx/           Scalar, vector, matrix math
  sokol/gx/          Sokol graphics bindings
docs/                 Documentation
Architecture
GX Source (.gx)
    |
    v
[ Frontend ]
  Lexer -> Parser -> Comptime Pre-pass -> Resolver -> Type Checker -> Comptime Post-pass
    |
    v
[ Backend ] (pluggable)
  C Backend: transpile to C -> invoke C compiler -> native executable
The compile-time system runs in two passes: pre-pass (before resolution — #if pruning, struct expansion, template monomorphization) and post-pass (after type checking — #for unrolling, reflection evaluation).

Status
v0.3.0 — Compile-time system complete. Templates, reflection, build directives, struct/enum expansion. Pointer arithmetic. 36 examples, 28 tests.

License
MIT
