# 🧠 MiniImp Project: A Compiler and Interpreter for the MiniImp Language
The MiniImp project is a comprehensive implementation of a compiler and interpreter for the MiniImp language, a simplified programming language designed for educational purposes. The project provides a robust and efficient way to compile and execute MiniImp programs, making it an ideal tool for students and developers alike. The core features of the project include a parser, a control flow graph generator, a register allocator, and a live variable analysis module.

## 🚀 Features
* **Parser**: A parser for the MiniImp language, capable of parsing source code and generating an abstract syntax tree (AST)
* **Control Flow Graph Generator**: A module that generates a control flow graph (CFG) from the AST, allowing for analysis and optimization of the program
* **Register Allocator**: A register allocator that assigns registers to variables and generates code to spill registers to memory when necessary
* **Live Variable Analysis**: A live variable analysis module that determines the set of variables that are live at each point in the program
* **Compiler**: A compiler that takes a MiniImp source file as input and generates a translated output file
* **Interpreter**: An interpreter that takes a MiniImp source file as input and executes it

## 🛠️ Tech Stack
* **OCaml**: The programming language used for the implementation of the project
* **Dune**: The build system used for managing the build process
* **Menhir**: The parser generator used for generating the parser
* **OCamllex**: The lexer generator used for generating the lexer
* **Hashtbl**: The hash table module used for storing variable bindings and other data structures

## 📦 Installation
To install the project, follow these steps:
1. Install the Dune build system and the OCaml compiler
2. Clone the repository and navigate to the project directory
3. Run the command `dune build` to build the project
4. Run the command `dune install` to install the project

## 💻 Usage
To use the project, follow these steps:
1. Compile a MiniImp source file using the command `dune exec ./bin/MiniImpCompiler.exe -- <input_file>.mimp`
2. Execute a MiniImp source file using the command `dune exec ./bin/MiniImpInterpreter.exe -- <input_file>.mimp`

## 📂 Project Structure
```markdown
.
├── bin
│   ├── MiniImpCompiler.ml
│   └── MiniImpInterpreter.ml
├── lib
│   ├── MiniImp.ml
│   ├── MiniImpControlFlow.ml
│   ├── GenericCFG.ml
│   ├── RegisterAllocator.ml
│   └── LiveVariableAnalysis.ml
├── dune
├── dune-project
└── README.md
```


## 🤝 Contributing
To contribute to the project, please fork the repository and submit a pull request with your changes. Make sure to follow the coding standards and guidelines set forth in the project.

## 📝 License
The project is licensed under the MIT License.

## 📬 Contact
For more information or to report issues, please contact us at aldomob00@gmail.com.
