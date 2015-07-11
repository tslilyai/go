#Official Toolchain
- Low Level (compiler/linker): access via `go tool <tool_name>`
  - Compilation: `go build -gcflags <flags>` → passes options to `go tool compile`
    - `go tool compile -h` → see options (1.5)
    - Linker: `go build -ldflags <flags>` → passes options to go tool link
- High level (go tool)
    - gc, gccgo compilers subcommands: get, build, test, generate, etc.
    - `-x` print low-level cmds as run
    - `-n` same as `-x` but don’t run cmds

####Compilers:
- gc suite (official/most mature)
  - Go1.5 → completely written in Go
  - compile and link tools, handle all supported targets
  - nacl support (sandboxed binaries, native client)
  - access to high-level optimizations (some Go specific)
- gccgo
  - Go frontend for GCC compiler suite, 2nd most mature (behind official compiler)
  - targets GCC’s supported architectures
  - GDB support, RTEMS support
  - optimizations → more low level rather than high level, might not be as fast as CPU predictions
- llgo
  - Go frontend for llvm/clang, imcomplete
  - access to PNaCL and asm.js

####Transpilers:
- GopherJS (Go → JS)
    - fast JS code, good JS interoperation support, sourcemap support
    - large output → should only use for single-page apps
- TARDIS Go (Go → HaXe → C++/C#/Java/PHP/JS/etc.)

####Cross compiling
- golang.org/pkg/go/build/ and tip.golang.org/doc/install/source#environment
- compiler doesn’t care what build platform is, only what target platform is (every compilation is a cross-compilation)
- Go 1.5 has cross-compilation capabilities built-in (not separate for each target arch)
- environment variables control build targets