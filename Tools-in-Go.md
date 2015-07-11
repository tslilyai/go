See [[Vim with Go]] for how to get go tools integrated with Vim. (Sorry emacs users)

#Index
- [Debugging](#1)
- [Documentation](#2)
- [Code Generation](#3)
- [Mobile](#4)
- [Refactoring](#5)
- [Static Analysis](#6)
- [Style](#7)
- [Useful/Random](#8)

##<a name=1>Debugging Tools</a>
####[Delve](github.com/derekparker/delve)
gdb-like debugger specific for Go. See Gophercon talk [here](https://github.com/thumbtack/wiki/wiki/Gophercon-2015#3)

####Data Race Detector
- Data Race: when two goroutines access the same var concurrently, at least one accesses is a write => compromise memory/type-safety of program (non-deterministic)
  - compilers assume race-free programs, do aggressive optimizations
- add `-race` to `test`, `build`, `install` commands to detect data races
- How it works:
  - instrumentation: compiler instrumentation pass enabled by `-race`
  - run-time module handles memory accesses, synchronization, function calls, goroutine creation/exit
    - algorithm based on dynamic modelling of happens-before relation
    - no false positives, false negatives possible
- Write good concurrent test, have continuous build with race detector, run integration tests, run race-enabled canaries in production

####Go-fuzz (randomized testing system)
- Randomized testing, intended mostly for programs that parse complex inputs
  - random blob of data --> feed into program --> look for crashes --> profit
- Generate diverse but *meaningful* inputs with coverage-guided fuzzing (genetic algorithms)

```bash
> go get github.com/dvyukov/go-fuzz
> go-fuzz-build <file>
> go-fuzz -bin=gob.fuzz.zip -workdir=<file_dir>
```

####Execution Tracer
- `-trace`
- Gives insight into dynamic execution of a program (goroutine creation, events, stack traces, etc.) 

##<a name=2>Documentation Tools</a>
- [`godoc`](http://godoc.org/golang.org/x/tools/cmd/godoc): use to get http documentation
- [`go doc <pkg>.<func>.<...>`](http://golang.org/pkg/go/doc/): show documentation for function or package
- `gddo`: server for http://godoc.org (run internally on godoc.thumbtack.io)

##<a name=3>Code Generation Tools</a>
- [`go generate`](http://blog.golang.org/generate): result is comment in code like `//go:generate command arg1 arg2`
- [`stringer`](https://www.godoc.org/github.com/Go-zh/tools/cmd/stringer): generates String() method for type with iota constants
- [`gen`](github.com/clipperhouse/gen): generates methods for performing LINQ-style queries on arbitrary types
- [`re2dfa`](github.com/opennota/re2dfa): generates finite state machines from regexp input

##<a name=4>Mobile Tools</a>
- [`gomobile`](https://github.com/golang/mobile):
  - `gomobile init`
  - `gomobile -target={android/ios} {build/install}`
  - `gomobile bind` for language bindings
- see Gophercon talk [here](https://github.com/thumbtack/wiki/wiki/Gophercon-2015#14)

##<a name=5>Refactoring</a>
- Use `gofmt`, e.g. `gofmt -r '(a) -> a'` (replace all unnecessary parentheses)
  - example [here](http://go-talks.appspot.com/github.com/xtblog/gotalks/tools.slide#35)
- [`gorename`](golang.org/x/tools/cmd/gorename): enabled in `vim-go` plugin
- [eg](https://godoc.org/golang.org/x/tools/refactor/eg): example based expression refactoring
  - example [here](http://go-talks.appspot.com/github.com/xtblog/gotalks/tools.slide#37)
- [go fix]https://golang.org/cmd/fix/: finds Go programs that use old APIs and rewrites them to use newer ones

##<a name=6>Static Analysis Tools</a>
- [`go vet`]: finds semantic mistakes in code
- [`oracle`](golang.org/x/tools/cmd/oracle): shows object types, methods/fields, callers/callees, ptr logic, channel peers, and much more!
- [callgraph tool](golang.org/x/tools/cmd/callgraph)
  - [digraph tool](http://godoc.org/golang.org/x/tools/cmd/digraph): modify call graphs, dependency graphs with quoted labels, etc.

##<a name=7>Style Tools</a>
- [`gofmt`](https://golang.org/cmd/gofmt/): formats Go code according to style standards
  - `go fmt`: wrapper for `gofmt -w -l *.go`
  - `-l` input files that will be changed
  - `-d` print diffs
  - `-e` print all errors
  - `-w` overwrite input files
  - `-s` simplify expressions (can make backwards-incompatible)
- [`goimports`](https://godoc.org/golang.org/x/tools/cmd/goimports): superset of gofmt functionality, provides import management (*warning: may resolve pkg names incorrectly!)
- [`go lint`](https://github.com/golang/lint): list style suggestions/mistakes

##<a name=8>Useful/Random Tools</a>
- [`present`](http://godoc.org/golang.org/x/tools/cmd/present): runs a web server that presents slide and article files from the current directory
- Translation Tools:
  - C: [cgo](golang.org/cmd/cgo/) and [swig](www.swig.org/Doc2.0/Go.html)
  - [`gobind`](golang.org/s/gobind): Java and Objective-C code