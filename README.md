# MiniLang — A Tiny Language Playground

This is my OCaml project from university. It has interpreters for two small languages and a compiler that generates RISC-like assembly code from one of them.

## What's here

There are three languages, each building on the last:

| Language | What it is | What you can do with it |
|----------|-----------|------------------------|
| MiniFun  | A functional language with closures, recursion, first-class functions | Interpret it |
| MiniTyFun | Same as MiniFun but with type checking | Check types + interpret it |
| MiniImp  | An imperative language with variables, loops, if/else | Interpret it OR compile it to RISC assembly |

## The compiler pipeline

The interesting part is the MiniImp compiler. It takes your imperative program through several stages:

```
Source code → Parse → Control Flow Graph → 
Defined variables check → RISC translation → 
Live analysis + optimization → Register allocation → Assembly output
```

Each stage does one specific thing:

| Stage | What it does |
|-------|-------------|
| **Parse** | Turns source text into an **AST** (Abstract Syntax Tree) — a structured tree representation of the program, like a blueprint |
| **Control Flow Graph** | Breaks the AST into **basic blocks** (straight-line code chunks) connected by jumps and branches. The graph makes it possible to analyze how execution can move through the program |
| **Defined variables check** | Walks the CFG looking for variables that might be read before they've been assigned. Catches a common class of bugs at compile time |
| **RISC translation** | Converts each MiniImp operation into one or more RISC-like instructions using **virtual registers** — there's an unlimited supply of these, so no need to worry about constraints yet |
| **Live analysis** | Figures out, for each instruction, which virtual registers hold values that are still needed later. Registers that are "dead" (their value won't be read again) can be reused |
| **Live range optimization** (optional, `-op`) | Merges virtual registers whose lifetimes don't overlap. This reduces the total number of registers needed before the mapping to physical ones |
| **Register allocation** | Maps the unlimited virtual registers down to the limited physical registers you gave (the `N` argument). If there aren't enough, some values get **spilled** to memory — stored on the stack and loaded back later |

### The target ISA

The compiler generates code for a **custom teaching RISC ISA** — it's not real RISC-V or MIPS, just a simplified instruction set designed for this project. Instructions like `copy`, `loadi`, `addi`, and `store` work on numbered registers (`r1`, `r2`, ...) and there's a special `in`/`out` mechanism for I/O. There's no hardware that runs this directly — the to-do section mentions an emulator as the next step.

### The register count (`N`)

The first argument to the compiler is the number of **physical registers** available. This tells the allocator how many real slots it has to work with:

- **More registers** → less spilling → faster (hypothetical) execution
- **Fewer registers** → more spilling to memory → slower, but may still compile

You need at least 2 registers for compilation to work at all; I recommend 4+. With too few, the compiler will spill aggressively or fail on complex programs. For simple test programs, 4 is usually plenty. Try experimenting: `MiniImpCompiler 4`, `8`, `16` on the same input and compare the output.

### Features you can toggle

- **`-d`** — Debug mode. Prints the CFG, the RISC graph, and the register allocation at each stage. Useful to understand what's happening.
- **`-op`** — Live range optimization. Merges registers that don't overlap in their live ranges to reduce register pressure.
- **`-uc`** — Undefined variable check. Detects variables used before being assigned anywhere in the program.

### Example

Input program:

```
in -> x;
out <- y;
x := 5;
y := 0;
while (0 < 10) {
  x := x + 1;
  x := x * 2;
  if (x < 7) then {
    x := 100
  } else {
    x := x + 1
  }
};
y := x
```

Compiled with `MiniImpCompiler 8 program.mimp output.txt -d -op`:

```
main:	copy in => r4
	loadi 5 => r1
	loadi 0 => r5
	...
l4:	loadi 0 => r1
	addi r4 5 => r7
	copy r7 => out
	...
```

## How to build

You need OCaml and Dune installed.

```bash
# Install dependencies (macOS with Homebrew)
brew install ocaml opam dune
opam install menhir ppx_deriving stdio

# Build everything
dune build

# Run tests
dune test
```

## How to use

### Interpret a MiniFun program

```bash
dune exec ./bin/MiniFunInterpreter.exe -- program.mfun
```

### Interpret a typed MiniFun program

```bash
dune exec ./bin/MiniTyFunInterpreter.exe -- program.mfun
```

### Interpret a MiniImp program

```bash
dune exec ./bin/MiniImpInterpreter.exe -- program.mimp
```

### Compile a MiniImp program

```bash
dune exec ./bin/MiniImpCompiler.exe -- 8 program.mimp output.txt

# Or with options
dune exec ./bin/MiniImpCompiler.exe -- 8 program.mimp output.txt -d -op -uc
```

The first argument (8) is the number of registers available on the target machine. The compiler needs at least 4.

## Project structure

```
bin/              — Entry points for each tool
  MiniImpCompiler.ml       — The compiler (main file)
  MiniImpInterpreter.ml    — MiniImp interpreter
  MiniFunInterpreter.ml    — MiniFun interpreter
  MiniTyFunInterpreter.ml  — Typed MiniFun + type checker

lib/              — Library modules
  MiniFun.ml / .mli        — MiniFun AST + evaluator
  MiniFunLexer.mll         — Lexer
  MiniFunParser.mly        — Parser
  MiniImp.ml / .mli        — MiniImp AST + evaluator
  MiniImpLexer.mll         — Lexer
  MiniImpParser.mly        — Parser
  MiniImpControlFlow.ml    — Builds CFG from MiniImp AST
  GenericCFG.ml            — Generic CFG data structure
  CFGDataFlowAnalysis.ml   — Generic data-flow analysis framework
  DefinedVariablesAnalysis.ml — Detects undefined variables
  MiniRISC.ml              — RISC instruction set + printer
  MiniRISCControlFlowGraph.ml — Translates MiniImp CFG to RISC
  LiveVariableAnalysis.ml  — Liveness analysis on the RISC CFG
  LiveRangeOptimization.ml — Merges non-overlapping live ranges
  RegisterAllocator.ml     — Maps virtual to physical registers + spill

test/             — Tests (WIP)
```

## What I'd still like to add

- Proper test files with MiniImp/MiniFun examples
- An emulator for the generated RISC code (right now there's no way to run the output)
- Better error messages
- More optimizations (constant folding, dead code elimination)

## License

MIT
