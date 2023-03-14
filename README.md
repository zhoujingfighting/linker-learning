## what is linker

A linker is but a small part of a toolchain, the low-level programmer's toolbox:
everything you need to go from source code to execution.

* The crown jewel of any toolchain is the compiler. The LLVM toolchain, for example, include Clang, a C/C++ compiler

* Another toolchain program, assembler(汇编器), assembles each `.s` file into a `.o` file, an object file. An object file is merely a textual 
representation of an object file;

* Linker, links all of your objects file into a final `executable` or `binary`, traditionally given the name `a.out`

### Final link action

The "final link" incorporates several `.o` files and `.a` files to produce an executable. It does roughly the following:

* Parsing all the objects and static libraries and put their `symbols` into a database. Symbols are named addresses of functions and global variables

* Search for all unresolved symbol references in the `.o` files and match it up with a symbol from the databses, recursively doing this for any code in `.a`
referenced during this process, This forms a sort of dependency graph between sections. This step is called `symbol resolution`

* Throw out any code that isn't referenced by the input files by tracing the dependency graph from entry-point symbol(eg., `_start` on `Linux`), This step
is called `garbage collection`

* Execute the linker script to figure out how to stitch the final binary together. This includes discovering the offsets at which everything will go

* Resolve `Relocations`, "holes" in the bianry that require knowing the final runtime address of the section. Relocations are instructions placed in the object file for 
the linker to execute

* Write out the completed binary

> This process is extremely memory-intensive(非常耗费内存), sometimes, we saw the "fail to link" output in the console just because the linker exhausts the system's memory

### Object files and Sections

Linkers, fundamentally, consume object files and produce object files, the output is executable, meaning that all relocations have been resolved and an entry-point address(where the OS/bootloader will jump to to start the binary)

* Allocatable(ALLOC) sections must be `allocated` space by the operating system;

* if a section is loadable(load), then the operating system must further fill that space with the contentsof the section, this process is called `loading` and is performed by a `loader program`, The loader is sometomes called dynamic linker, and is often the same program as the "program linker"; this is why the linker is called `ld`.


some common(POSIX) sections include:
* `.text`, where your code lives. It's usually a loadable, readonly, executable section.
* `.data` contains initial values of global variables. It's loadable
* `.rodata` contains constants. It's loadable and readonly
* `.bss` is an empty allocatable section. `.C` specifies that uninitialized globals default to zero; this is a convenient way for avoiding storing a huge block of zeros in the executable!
* Debug sections that are not loaded or allocated;these are usually removed for release builds.

After the linker decides which sections from `.o` and `.a` inputs to keep (based on which symbols it needed), it looks to the `linker script` how to arrange them in the output.




