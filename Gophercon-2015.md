Slides from the talks can be found [here](https://github.com/gophercon/2015-talks)

#Index
###Day 1
- [Opening Keynote](#1)
- [Go Kit: A Standard Library for Distributed Programming](#2)
- [Delve Into Go](#3)
- [Beginning to Program with Go](#4)
- [A Practical Guide to Preventing Deadlocks and Leaks in Go](#5)
- [Go GC: Solving the Latency Problem](#6)
- [Simplicity and Go](#7)
- [Rebuilding Parse in Go](#8)
- [Prometheus: Designing and Implementing a Modern Monitoring Solution in Go](#9)
- [What Could Go Wrong (at Runtime)](#10)
- [The Roots of Go](#11)

###Day 2
- [The Evolution of Go](#12)
- [Static Code Analysis Using SSA](#13)
- [Go for Mobile Devices](#14)
- [Go Dynamic Tools](#15)
- [Embrace the Interface](#16)
- [Uptime: Building Resilient Services](#17)
- [Cayley: Graph Database](#18)
- [Code Generation in Go](#19)
- [Struct Tags](#20)
- [CoreOS: Betting on Go and Winning](#21)
- [Closing Keynote](#22)

*Notes taken by LYT (sorry for typos)*

***
#Day 1

#<a name=1>Opening Keynote</a>
Ross Cox: tech lead for Go project
####Success of Go:
- Concurrency software (Cloud software) => distributed systems/cloud-centric
- software for Collaboration/OpenSource => growing importance

####“Do Less. Enable More”
- Make scalable concurrency and collaboration easier: try to do just enough (minimalist) so programmer can easily use Go
keep size of language small, always simplify and reach consensus on a change improves/benefits design => essence of basic concepts
- willing to sacrifice specific use cases to avoid bloating language
- examples
  - channels/goroutines => balance btwn high and low level
  - types/interfaces => statically typed for compile-time checking, but more lightweight
  - testing/benchmarking => not comprehensive but basic groundwork, works for all pkgs
  - gofmt to keep everything uniform => allow for automated changes!
  - go get, path imports, godocs
- Establishing shared conventions (“opinionated”) => enable tools to interoperate!

####Go and Open Source
- only way Go can succeed => closed languages die? needs a large community to live
- tools/libraries that are needed are most likely already created, bugs id and fixed more easily
- Go compilers more robust than C compilers, Go not directed toward a specific use case
- Google team too small => never scales, role is to communicate vision for Go to community

####Focus on expanding the type of work Go can enable
- Vendoring: only one version of library in source tree and update vs. always using newest library
  - tools to vendor can’t work together/fragment development community => 1.5 has vendoring
  - enable switching btwn tools, go get and godep to interoperate
- Need Go Code of Conduct => people who want to improve/discuss Go

***

#<a name=2>Go Kit: Std Library for Distributed Programming</a>
Peter Bourgon
**http://peter.bourgon.org/go-kit/**

##Modern Enterprise:
- tech-oriented, consumer-focused, exponential growth, 50-1K eng, microservice architecture (SOA)
- Twitter, Netflix, Spotify, SoundCloud
- take risks on new software --> incentivized
- need set of code, tools ,idioms, best practices
  - Go not successful for these enterprises
    - immature, lack of higher-order library support
    - Go is the language of the server --> should become language of modern enterprise

## Go kit:
- higher-order abstractions for microservices
- strong idioms and opinions
- Netflix's Ribbon, Twitter's Finagle

#### Goals:
  - make Go a first-class citizen at application layer:
    - border (nginx)
    - application - go kit
    - data
  - target microservice architecture
  - RPC messaging pattern
  - mixed environment
  - pluggable transports: logic decoupled from access
  - encourage best-practices

#### Non-goals:
  - require specific infrastructure
  - opinions about orchestrations, services run/deployed/supervised however organization wants
  - opinions about configurations (flags/env vars/conf files, etc)

#### Components:
  - Service: receives messages, sends messages, requires request tracing, logging, metrics, rate limiting, LB, circuit breaking, service discovery
  - Go kit @ service level: service connectivity/safety/metrics/transport (programmer: analytics, metrics, logic)
  - package endpoint: type endpoint, middleware --> use in both servers and clients
  - transport library (bindings)
  - package log: type logger (mandatory structured logging)
  - package metrics: type counter, gauge, histogram
  - package ratelimit: returns a middleware
  - package circuitbreaker: returns a middleware, critical for large infrastructures
  - package loadbalancer: servic discovery, types publisher and loadbalancer
  - package tracing: annotating server/client, returns middleware 

####Example:
- wrap addFunc with endpt, end pt with a binding, expose binding according to rules of transport
- adding Logging: define business middleware --> takes addFunc, returns an addFunc (access to addFunc params)
  - composable middleware, declarative composition of function or endpt middlewares --> chaining techniques

####TODO:
- tools to generate adapters and bindings, expand example coverage
- support more messaging patterns

***

#<a name=3>Delve Into Go: Debugger for Go</a>
- Derek Parker, Pre 1.0: active development written in both Go and C
- Supports Linux/OS X, integration with editors (vim)
- Future: 1.0 release, use as developer toolkit, support for Windows and editor integration, advanced commands

####Why We Need Delve 
- Existing tools are old, don't work => meant for C and C++
- Need Go-centric tool, built specifically for Go nuances
- Need a tool easy to use (like all Go tools)

####Go-Specific Optimization 
- Execution Model:
  - defer statements: GDB/other tools don't recognize/follow execution flow
  - threads vs. goroutines: 
    - threads = smallest unit of execution recognized by existing tools
    - goroutines scheduled on top of threads (green threads)
  - Go scheduler
    - Processor: exectuion context w/runnable goroutine queue
    - OS Threads, Goroutines
    - Context switches: switch between goroutines (can be picked up by another thread)
      - channel ops, syscalls, runtim.Gosched, GC, Go statement
      - causes GDB to hang/be confused
- Stack Management
  - Runtime stack inspection
  - Function Prologue Stack Check: goroutines intialized with 4k stack, check for stack growth => confuses debuggers (creates dummy stack frames)
- Compiler Optimizations
  - Function inlining: GDB relates location of code to locaiton in source code
  - Registerizing Variables: GDB can read variables, get garbage back
- Solution: pass in flags to Go compiler (complicated!)

####Delve
- Parsing information out of Go binary, coordinates w/OS and CPU to manipulate program
- No source rewriting, similar to GDB
- Compiles code w/ optimizations disabled, starts program, attaches to process, land you at prompt to find bugs
- Debugs standalone programs, existing binaries, running processes

####Commands
- Control Flow: breakpoints, tracepoints, continue, single step (1 CPU instruction), next source line, switch threads, restart
- Informational: threads, goroutines, breakpoints, print <var>, info
- Subcommands: args, funcs, locals, sources, vars, regs
- runtime.Breakpoint => place in source code

***

#<a name=4>Getting Started with Go</a>
Audrey Lim

####Go as a Teaching Tool 
- *The Way to Go* - Ivo Balbaert
- Clear conventions, only one way to do one thing, no worry about syntax issues
- Syntax: exposes programming concepts
- Mini challenges
- Projects (CRUD, API Client, Raspberry Pi app, Micro-controller library, Markdown Parser, Grep in Rust)
  - vspark: Go wrapper around C program to control Spark Core

###Imagination
- Tools shape the way you think/character of the programs end up building

***

#<a name=5>Preventing Deadlocks and Leaks in Go</a>
- Richard Fliam
- many long-lived goroutines can cause panics (panic: all goroutines are asleep (deadlock))

###CSP: communicating sequential processes
- Communicate by passing messages (share memory by communicating)
- Sequential: one thing at a time (avoid concurrency in process itself)
- Processes:
  - not types (not like OO design)
  - Hoare: mathematical abstraction of the interactions btwn a system and its env (i.e. vending machine)
    - inputs/outputs, steps it executes, NOT its attributes

###Tips for CSP Design
####1. Data Flow: inputs, outputs, and how they are connected
  - focus on flow of data, not flow of control (look at exposed channels, input/output)
  - process has decision tree, but communication btwn processes require data flow
  - control flow = single process
    - multiprocess control flow would require asking whose in charge, signaling for actions => "manager control flow"
    - hard to follow, jumping around (like GOTO)
    - overdependence on signals, callbacks
  - decouple processes => simpler and less interreliant, more reusable, easier to follow

####2. Drawing
  - connection diagrams: process/goroutines (box) and arrows/channels (directions of communication) 
  - God Processes (too busy, many connections), Rats Nest (high connectivity, muddle responsibility), Cycles (possible deadlocks)

####3. Pipelines
  - unidirectional data flow (composition/decomposition) (DAG)
  - cannot deadlock!

####4. Exiting
  - must have an exit strategy => cannot let exiting deadlock
  - cannot let exiting leak => if receiver exits before sender sends, sender is left hanging
  - use net/context library => pass context around, has Done method, channel closed when context is cancelled
    - can stop set of operations at once
  - collecting resutls: cancel => collect => exit
    - keep count of outstanding operations
  - notify children in pipeline to exit in order

####5. Avoid Leaks
  - Don't use Tickers
  - Close contexts (must call cancel()! use `defer cancel()`)
  - Defer properly (don't `defer` inside infinite loop)
  - Finish your goroutines (must `return`!)

###TV Example
- Muxing: join separate pieces of data into single stream (signals)
  - chop data into packets, chain data from different pieces together
- ID inputs (TV streams) and outputs (video/audio for each channel), draw out process data flow

***

#<a name=6>Go GC: Solving the Latency Problem</a>
- Rick Hudson
- 1.5: Trade throughput for reduced latency
- Go draws upon C to GC and allocate (reduce fragmentation by looking @ malloc, etc.)

####Java isn't Go:
- Want to build a GC for Go, cannot use Java GC
- Thousands of goroutines vs. tens of javathreads
- synchronization via channels vs synchronization via objects/locks
- Runtime written in Go vs Runtime written in C
  - Go runtime leverages Go same as users
- Control of spacial locality (objects can be embedded, terior pointers), Java objects linked with ptrs

####GC Flow 
- GC prepares to run: stop all goroutines and get ready to do garbage collection (submillisecond), write barrier turns on
  - app tells GC which ptrs are created with write-barrier (*more expensive* than just stopping the app, slows things down)
- scan phase: look at ptrs in stacks/registers, globals to see how application can get into the heap
  - write barrier on
- mark phase: take pointers, see which objects in heap are reachable
  - write barrier on
- mark termination and stop the world for rescan, finishing marking, etc., turn write barrier off
- sweep phase: unmarked objects freed, app can reuse and reclaim
- repeat

####GC in Go1.5
- 1.4: GC uses all CPUs, 1.5 GC given 25% of CPUs
- Application upon allocations assists GC, if not allocating then not disrupted!
- Only slows down resources trying to allocate!
- Increasing heap size == better performance (throughput decrease is not too bad)
  - Not a problem in single-threaded program, compiler doesn't have to stop for GC

####Future
- GC not a barrier with Go's low latency
- will be improved in the future

***

#<a name=7>Simplicity and Go</a>
Katherine Cox-Buday

####Simplicity Fanatics
- Reacting, not thinking => don't simplify by coincidence (pragmatic programmer)
- Assuming idea of simplicity but not thinking things through:
  - Problem 1: Can we use a build system?
  - Problem 2: Can we use a framework?
  - Problem 3: Can we use design patterns?
  - Problem 4: Can we use dependency injection?
  - Problem 5: Can we use vendoring?

####Issues Clouding Go Simplicity
- "Pop Culture"
- Ease/Familiarity: New, small language, still possible to know just about everything about Go
  - people gravitate towards what they already know, reject frameworks
- Appeal to Authority: refer to Go standard library, "if Go std lib doesn't use, then we shouldn't"
- Trend to use small programs in large complex systems instead of using inversion of control

####Drivers of Go Simplicity
- Creaters of Go: wanted Go to be easy to use and easy to understand, even if that contradicts *superficial* ease of use
  - Another Go at Language Design
  - Simple Made Easy (easy =/= simple)
- Consensus over design => everyone had to agree feature was right, controls scope/simplicity
  - Trying to get it right the first time
- Incidental Complexity vs. Problem Complexity
- Language cannot be too radical (approachable)

***

#<a name=8>Rebuilding the Parse API in Go</a>
Abhisek Kona

####What is Parse
- mobile platform, backend as a service: can build app without servers
- acquired by FB in 2013, had ~60K apps, 10 engineers, Ruby on Rails app
- problems:
  - single popular app could take down Parse
  - fixed-sze unicorn worker pool per machine, could run out and autoscale couldn't respond in time
  - lengthy deploy times and tests
  - spooky action at a distance
  - hard to evolve the existing Ruby codebase
- rewrite: much more reliable/stable, easier to deploy, faster tests
  - save backwards compatibility (old apps), no downtime

####Why Go?
- statically typed
- good concurrency support
- dynamic number of worker goroutines per HTTP server
- easy to attract engineers

####Initial Ports
- Rewrote hosting server, Parse Push Notification service
- Maintaining lng-lived push sockets with Android clients
- Concurrent connections per node 6x
- Mongo Proxy: had to write own proxy for Mongo in Go, Mongo used to limit max nmber of connections
  - made easy by Go runtime's use of non-blocking I/O

####Rollout
- migrate endpoints one by one
- diff responses btwn old/new code using shadow cluster
  - Ruby supports weird formats of HTTP, Go doesn't => had to implement for backward support
- graduated to write endpoints

####Discoveries about Go
- mgo, memcachce drivers were great
- some missing libraries, had to build some:
  - dependency injection (inject), ensures have inject implementation for all interfaces
  - startstop: starting and stopping objects in order of dependency
  - grace: graceful restarts
  - stackerr: aggregates errors for error reporting
  - muster: perform operations in a batch
  - httpcontrol, ensure, stack

***

#<a name=9>Prometheus: Designing and Implementing a Modern Monitoring Solution in Go</a>
Bjorn Rabenstein, prometheus.io

####Metrics:
  - Metric Interface
  - Counter Interface
    - increments/writes goroutine-safe => mutexes
    - performance: monitoring should add very little overhead (add concurrent benchmarks)
      - increasing goroutines => lock contention
        - highly sequential, parallel in multiple cores

####Problems
- 1000s of goroutines all trying to implement one piece of memory
  -solution1: channels
    - one goroutine should receive messages from all other goroutines to increment the counter
    - numbers went up! :( applying high-level solution to low-level problem
  - solution2: sync/atomic (we use this)
- using floats for sample values
  - no tool to atomically inc a float
  - solution: save bits of float, save as UINT64, atomically increment UINT64, get floats from bits
    - not as nice as incrementing incs
- must 64-bit align 64-bit words on 32-bit architectures

####Lessons Learned
- use `-benchmem` to detect allocation churn: `go test -bench=. -cpu=1,4,16 -benchmem`
- escape analysis: `go test -gcflags=-m -bench=Something`
- use `pprof`
- Use `cgo` judiciously => loss of certain advantages of Go build env, per-call overhead (dominates run-time), need ot shovel input/output data

***

#<a name=10>What Could Go Wrong (at Runtime)</a>
Kevin Cantwell

- Simplicity of Go: small keyword space (25) (vs C++ with 84)

####Problems
- Ranging over strings
  - UTF8, ranging over unicode codepoints of strings
  - strings are byte slices => ranging over byte slices different than ranging over actual string
      - e.g. hearts are 3 bytes, but only one symbol as a string
- Ranging over maps
  - somewhat randomized order
  - modifying maps during ranging over maps => *nondeterministic* which elements of map are being modified if indexing into map
- Ranging with closures
  - enclosing variables enclose references, not copy, of variable
  - range loops use fixed value, not references, so closure within the loop enclosing variable will use unchanging value
  - solution: don't enclose variables
- Typed Nils
  - by declaring an error `err` as a ptr to a type ConstructedError that doesn't return `nil`, checks for `nil` fail
  - variable evaluates to `nil` **only if** underlying type is `nil` and value is `nil`
- Leaky Defers
  - function that doesn't return, `defer`s never occur!!!
  - wrap `defer`s in anonymous function, or don't use `defer` at all
- Slices are not Arrays
  - array are values in Go, not ptrs to values, cannot be modified by reference => must make copy
- Shadowing Embedded Types
  - No notion of subclassing, rather embedding of types
  - Must know which type's methods and fields are being used/called (`bar.foo.Val` vs `bar.Val`)
- Blocked Channels
  - Forgetting to select on a sending channel => receive happens before send completes
  - Should buffer the channel (`b := make(chan bool, 1)`)
- Blocked Scheduler
  - Goroutines scheduled on single thread w/CPU=1
  - no other goroutine executed if loop is run (delayed)
  - solution: yield to runtime scheduler `runtime.Gosched()` or with `sleep`
- Predeclared Identifiers
  - `int`, `iota`, `true`, `false`, `nil`, `bool`, etc. all predeclared
  - can be redeclared (i.e. reimplement `delete`)

***

#<a name=11>The Roots of Go</a>
Baishampayan Ghose

####Building on the Past
- Example: Need to build software, and then scale after produce
  - scaling should be easy, but doesn't really work => call it a "feature"
  - design is a very important part of software development, must design before code
- Make and fix paradigm => must go back to past and rebuild
- At scale, architecture dominates material
  - at large scale, doesn't matter what tools you're using, architecture matters more
  - architecture: art or practice of designing structures
- Doing less of the wrong things allows us to do more of the right things: should build on past langs
  - "Less is exponentially more"

####Arches of Go
- BCPL (Martin Richards '67)
  - Basic of CPL, focus on programmer efficiency, first `Hello World` program!
  - bootstrap-compiled (compiler written in source programming lang, self-hosted)
  - lexer, rules to injects semicolons at standardized places (parsing w/semicolons is much simpler/faster)
- Modula-2 (Niklaus Wirth '77)
  - packages/modules, exporting variables outside of package
  - exported symbols and related info for compiler so compiler knew what it had to link with
  - Modula-3: more similar to Go (had `IO` and `Fmt`)
- CSP (CAR Hoare '78)
  - Communicating Sequential Processes, sharing memory by communicating
  - change way look at IPC, channels as sync and communication
  - channels second class in CSP, first class in Go
- Newsqueak (Rob Pike '88)
  - GUI programs, pass lots of messages/interactive
  - introduced notion of first-class channels
  - "goroutines" => go keyword = begin
- Smalltalk (Alan Kay '76)
  - emphasize composition over inheristance, values at meta-receivers
- C, Pascal, APL: syntax, Semantics, Pragmatics
- LISP

***

#Day 2

#<a name=12>The Evolution of Go</a>
Robert Griesemer

####Personal Background
- interest in PL after encountered Pascal
- 15 years of using C++, programming in industry felt backward
- met Rob and Ken

####Starting Points
- Problem: complexity, missing concurrency support, lack of scalability, high build times, no closures/modules
- Goal: better language for Google than C++, small/clean/compiled lang with modern features
- Guiding Principles:
  - simplicity (minimal--one way to write piece of code), mem safety, readability
  - orthogonality in design
  - express algorithms, not type system 
  - things of interest should be easy even if sacrifice other features
  - literature on good language design (CAR Hoare)

####Day1 Ideas in Go
- syntax (keyword notation, cleanups)
- expressions :5 binary precedence levels
- explicitely sized basic types, rune types, no implicit conversions
- packages, imports, methods with receivers, interfaces

####PL Features Influencing Go 
- Many features taken from previous PL, consolidate them/distill them down
- Algol family (Algol 60) => block structure, nested/recursive functions, type declarations/static typing, etc.A
- Pascal, C => ALgol successors, direct impact on Go
  - Go is as much Pascal/Oberon as it is C
- Pascal successors: Modula/-2 (modules, separate compilation), Oberon/-2 (simplified modulers, classes/methods, methods on records/structs)
- syntax details are difference but structure is same: C tokens, Oberon structure
- same concepts (pkgs, imports, types, functions/methods, etc.)
  - only on loop construct in Go
- OO and Dynamic PL 
  - OO and generics becoming popular (C++, Java, etc.)
  - Dynamically typed interpreted languages (Erlang, Perl, Lua, JS, Ruby) more popula
    - OO hard to read, hard to maintain dynamically typed, much more notation
- Interfaces: Go needed *some* support for OO => turn to Smalltlak (everything is an object, any message/methods can be sent to any object)
    - **Crucial Insight**: attach methods to any type if interfaces carry type info rather than objects. *Only need methods and interfaces for OO programming*
    - class methods/instance methods can be captured in the notion of interfaces, no need for dynamic dispatches from every object (attach to interface, not object)
- Concurrency (NewSqueak, Rob Pike)
- Generics: single biggest language feature absent in Go
  - type-system mechanism, unclear if essential
  - incredibly complex in semantics and implementations (considerable tradeoffs: larger binary, slower binary, larger source code)
  - not orthogonal: affects several other language features, how libraries are written
  - stlil thinking about it

####Putting it all together
- 2 years to hammer out basics, added *one feature at a time*
- multiple people => different angles on each feature, made language stronger
- 3 frontends: Russ Cox, Ian Lance Taylor (Go copmiler), go/types (validation of sopmilers, spec)
  - able to find bugs in spec and language (diff frontends interpreted differently)

####Evolving Go
- original design went through many syntax/semantic transtions
  - parllel library development
  - gofmt and gofix for existing code => very easy to change/move language forward
- later features:
  - optional semicolons, optional types for copmosite literals, optional bounds, recover, etc.
  - semantic clarifications for maps ,chanels, etc.
  - small backward-compatible adjustments

####The Future of Go
- Successful? Clear target, solid implementation (language, libraries, tools), market readiness, technological breakthrough, language features w/out competitors
- Go has clear target, multi-paradgn, syntactically light-weight (appealing)
  - No competition: goroutines, interfaces, defer, fast compiler, gofmt, go build
  - Strong stnadard libraries, solid implementation
  - excellent documentation, online tools
  - no corporate marketing
- Go as mainstream? Unified community, cannot make mistakes => takes ~10 yrs for PL to become "established"
- expanding to more domains to become general-purpose?

####Pitfalls
- Language is frozen (form of "language design")
  - build tags, specialized comments, special import paths, canonical import path comments
  - internal packages
  - vendoring descriptsions
- mechanisms not part of language spec, may diverge/have different semantics on different platforms

####Algol => Pascal and C => Go

***

#<a name=13>Static Code Analysis Using SSA</a>
Ben Johnson

####Expanding Go
- Favorite part of Go is [[Go Toolchain]]
  - golang.org/x/tools
- Code generation
- Go subpackages (go/ast, go/doc, go/parser, go/printer, go/scanner, go/token)
  - AST is limiting (only shows structure of code), but most people use it over SSA

####Static Single Assignment IR (SSAIR)
- Shows how code flows: golang.org/x/tools/go/ssa (Alan Donovan)
- IR: Intermediate Representation (layer between source and machine code)
  - AST sits in source code/Go code
  - IR is high level (goroutines, functions, types) and portable
- SSA: static single assignment
  - cannot overwrite variables, only assign them once
  ``` 
  x_1 := 1
  y_1 := 2
  x_2 = x_1+y_1
  ```
  - makes analysis easier
- SSA Terminology:
  - program: colleciton of all packages in program
  - package: types, functions, top level constructs
  - functions: 
    -basic blocks (contiguous set of instructions w/out jumps), linked together through conditions/jumps
      - see as CFG (Control Flow Graph)
    - instructions: operations on values (assignment, etc.)
- Flows: lines between blocks, lines between variables and variable usage/assignment

####Tools built on go/ssa
- safesql: verifies query string arg is compile time constant, prevents SQL injection
- oracle: ptr analysis, works with interfaces, callers/callees, paths to function calls, channels, etc.
- gorename: safely renames identifiers, renames declaration as well as references, works across pkgs in GOPATH

***

#<a name=14>Go for Mobile Devices</a>
Hana Kim

####Background
- Mobile support was frequently requested
- some users built their own Go binaries for Android with gdo + external linking
- some Android Apps used Go even before Go 1.4 (Goandroid+Mandala, Camlistore android app)
  - Java UI communicates with Go process

####golang.org/x/mobile
- Goal: Bring Go to Mobile Platform
  - use Go to program a complete sytsem (server/client)
  - write a single cross-platform Go library
- Two ways of using Go
  - Native Apps: write whole app in Go, requires Go pkgs for graphics, event handling, audio, etc.
  - SDK Apps: write Android UI in Java, iOS UI in Objective-C/Swift, write common functionality in Go as a library
- Available: app, asset, gl, event, geom, audio, etc.
  
####Challenges
- Cross-platform APIs: work fo Android, iOS, Desktop environments
  - provide a rich set of APIs, following idiomatic Go style
- Dealing with Toolchain installation, cross compilation for GOOS/GOARCH combos, Android/iOS-specific build details
  - gomobile tool: simplify toolchain installation and app deployment
  - `gomobile init`, `gomobile -target=android/ios build/install`

#### Extending Go to other languages
- Go as a library: Go 1.5 can build Go programs as a lirary used by non-Go programs
    - shared library for dynamic linking (not possible for iOS => archive file for static linking)
    - functions marked with `/export cgo` annotations are callable.
- work with foreign languages (Cgo, JNI) => mapping data structures/functions btwn languages tedious and error-prone
  - `gobind` tool (golang.org/x/mobile/com/gobind) => automate language binding through code generation, subset of Go types
  - generate Java/Objective-C code
- Building and updating: 
  - `gomobile bind` commands: generates languages bindings for Go pkgs, compiles Go code to shared library, compiles generated target lang code, bundles everything into`.aar` file (android)
  - update `.aar` file: build script to update  gomobile bind`

####Example: Ivy app (android and iOS)
- < 2 hrs to port Go code to Java/iOS

####Future
- 1.4: Android support, can build Android apps, Android biulder (`gobind` tool avaialble), packages for cross-device apps
- 1.5: experimental support for iOS, iOS builder
  - Go programs as libraries, call Go functions from foreign langauges in clean ways `exectools`
  - better tools and more packages, experimenting iwth audio, sensor, sprite
- 1.6+: improvement in GL/UI pkgs, more APIs available to "pure Go" apps, testing/profiling/debugging, more platform support, type support, IDE integration

***

#<a name=15>Go Dynamic Tools</a>
Dmitry Vyukov
- wrote goroutine scheduler, network poller, parallel GC, concurrent sweeping, memory alocator improvements, sync primitives, race detector, blocking profile
- Thread/Address/MemorySanitizers

####Data Race Detector
- Data Race: when two goroutines access the same var concurrently, at least one accesses is a write => compromise memory/type-safety of program
  - compilers assume race-free programs, do aggressive optimizations (assumes ownership)
  - non-deterministic, hard to debug
- add `- race` to `test`, `build`, etc. commands to detect data races
- instrumentation: ocmpiler instrumentation pass enabled by `-race`A
- run-time module handles memory accesses, synchronization, function calls, goroutine creation/exit
  - algorithm based on dynamic modelling of happens-before relation
  - no false positives, false negatives possible
- only as good as your tests: write good concurrent test, have continuous build with race detector, run integration tests, run race-enabled canaries in production
- found several bugs in std lib, elsewhere

####Go-fuzz (randomized testing system)
- Randomized testing, intended mostly for programs that parse complex inputs
  - random blob of data --> feed into program --> look for crashes --> profit
  - cheap to use, no bias
- Generate diverse but *meaningful* inputs 
  - Mutations and sonar do low-level changes (bit-flipping)
    - Don't need to guess correct values if program already knows it, can get it from code itself (source code instrumentation)
  - XML input: high-level changes
    - versifier: reverse-engineers text protocol, learns its structure --> applies structural mutations to inputs
  - All are coverage-guided fuzzing (genetic algorithms) -- generate coverage as output to update new test cases
- 115 bugs in std lib, 43 bugs in golang.org/x/, 134 elsewhere
```bash
go get github.com/dvyukov/go-fuzz
go-fuzz-build <file>
go-fizz -bin=gob.fuzz.zip -workdir=<file_dir>
```

####Execution Tracer
- Gives insight into dynamic execution of a program (goroutine creation, events, stack traces, etc.) 
- `-trace`

***

#<a name=16>Embrace the Interface</a>
Tomas Senart

"I decided to use Java to solve a problem, now I have a problem factory"

####Flattening the Conceptual Hierarchy
- "If C++ and Java are about type hierarchies and the taxonomy of types, Go is about composition" ~ Pike
- Go rooted in simplicity => promotes simple constructs, but still must have abstraction
- Orthogonal features => build upon each other, can consider one at a time
  - real code: tangled, must see interdependencies

####Principles/Patterns
- Single Responsibility Principle: software should have a single responsibility
  - limit interactions between orthogonal pieces of code/features
- Interface Segregation Principle: interface code should not depend on methods it does not use
- Open/Close Principle: Softwrae Entities should be open for extension, but closed for source code modification

####Achieving Principles
- Reduce the scope of interfaces => small and precise (single-method interfaces most composable and useful)
    - coexist with functions, use method to call themselves
- Decorator Patterns: decorators wrap interfaces with extra behavior: `type Decorator func(Client) Client`
  - use decorators: Logging, Instrumentation, Fault Tolerance, Authorization, Headers, Load Balancing
    
```go
    func Instrumentation(args) Decorator {
      return func(c Client) Client {
        ...
      }
    }
```
- Decorate function => decorates clients with all given decorators
- Middleware Go patterns => form of the decorator pattern

***

#<a name=17>Uptime: Building Resilient Services</a>
Blake Caldwell

####KilnProxy
- SSH remote proxy
- HTTP/SSH when using git to interact with remote server
- use SSH => talk to Kiln Proxy => finds out which backend server is yours => proxies info back and forth
- rewrite in Go b/c SSH clones slower than HTTP, stability issues
  - goroutine to accept SSH connection, authenticate via public private key, connect to backend server, proxies requests each w/own goroutine
  - repo clone times halved, less noise

##Resiliency: The Process
Resilient: Doesn't leak memory, safe, doesn't hang (development, profiling, monitoring)

####Careful Coding
- Error Handling and Clean-up
  - handle every error, check every error, remember to defer closes after error checks
- Nil checks: check for nil even if return value is not an error (ptrs may return nil)
- Channels: 
  - send to nil channel blocks forever
  - receive from nil channel blocks forever
  - send to closed channel panics
  - receive from closed channel returns zero value immediately
- Panics: due to programmer error, crashes service
  - possible to recover from panics, only recover if sure it's ok, panic recovery is for current goroutine
  - make sure to clean up function with defers
- Avoid Race Conditions: Go's Race Detector
- Implement Timeouts: network dial timeout, network conn inactivity timeout, total connection timeoutA
- Test Everything! Unit, integration tests

####Know Your Service
- Profiling (pprof)
  - memory service use when idle, per conn, reclaimed memory, GC, where memory allocated
  - blocking profile, goroutine count/stacktraces, heap profile, thread creations
  - goroutines when nobody connected, goroutines per connection, goroutines cleaned up after conection over?
- Watch it Run (in production)
  - /info Endpoint => return version, start time, current time, uptime
  - manage versioning
  - /connections Endpoint => see all currently connected clients and conn info
  - implement Drain and Die => admins can finish serving all connections and then terminate application
    - stop listening for new requests, sleep and loop until all existing requests are done

***

#<a name=18>Building a Graph Database</a>
Barak Michener

SKIPPED, see slides online

***

#<a name=19>Code Generation for the Sake of Consistency</a>
Sarah Adams

####Motivating Project
- Wearable device: baby monitor anklet, collects sensor data, pushes up to server, predictions
- Many teams working on app (data science, iOS, etc.) => many data formats
- Data (json) => server (Go struct) => database (Go structs) => instance of Obj-C class
- Require consistency across all pieces => communication across teams
  - Problems: team wouldn't read docs b/c sometimes docs are inaccurate

####Solution
  - docs must be 100% accurate => make computer do it!
    - (failed solution) docs = accuracy tests => inconsistent builds, when tests failed, had to manually update docs
      - needed to update docs when changed methods, renamed things, etc.
  - Design Docs
    - *The Mythical Man Month*: idea of a design doc
    - created Endpoint Design Doc: generic definitions of endpoint behavior
      - behavior consistency, makes API docs obsolete => can predict endpt behavior based on initial endpt
      - could generate requests and responses automatically, not business logic
  - Endpoint Consistency Tests: check endpts follow design docs
    - can generate automatically from design docs
  - API Docs: ensure accurate, place to look up endpt behavrio when can't predict it
    - can generate API docs from tests and design docs, ensures accuracy
    - list of requests and responses, list of metadata, etc.

***

#<a name=20>The Many Faces of Struct Tags</a>
Sam Helman and Kyle Erf

####Struct Tags
- String literal field that becomes an attribute for all fields in the corresponding field declaration => take part in type identity for structs
  - single struct tag per field

  ```go
  type MyType struct {
    Field string `field`
  }
  ```
  - Use to attach arbitrary values to fields, provided w/out a specific use case (no size limitation)
- Namespacing: mechanism for allowing multiple tags to be associated with the same struct field
`Id int `json:"id" bson:"_id"``
  - not included in the spec, not caught by compiler => runtime errors when using flags
  - catch errors with `go vet`, built into the `reflect` pkg

####Tag Safety
- Problem: need to reference the content of a tag (in MongoDB, ORMs, etc.), use struct tag to build queries
- Check tags at program initialization (like regex.MustCompile => MustHaveTag)
  - programatically access struct tags, create string constants based on value of tag

####Uses of Struct Tags
- Serialization
  - Most common use-case of struct tags (json, bson encoding)
    - allow something w/diff naming paradigms to communicate, map btwn other naming schemes and Go structs
    - XML, bson, sql, etc.
  - Unusual Mapping Case
    - mapstructure: converting between structs and Go's builtin `map`
    - config or encoding that changes slightly depending on specific fields
      - decode data into a map, then pull it into a struct based on its values
- Command-Line Configuration: alternative to Go's builtin flag pkg, uses struct tags to specify everything
  - parse flags into struct (go-flags)
- Default Values: simplifies initializers, initialization logic inside type definition (go-defaults) (``default:"1"``)
  - loses compile-safety, struct tags are just strings
- Validation: specify in struct tag how to validate a field (``valid:"email"``)
- Type Polymorphism: interface{} with type bounds, use struct tags to tell program what types a field can be
  - ``types:"int,error"``, run validation at runtime
- Foreign Languages: use struct tags to specify the translation of field names in variaous languages for printing/serializing
  - ``lang_es:"nombre"``
- Code Generation: use struct tags instead of comments for code generation, can attach struct tags to unexported fields

```go
type Voter struct {
  email string `generate:"get,validate-email"`
}

//generated code
func (v *Voter) GetEmail() string {return v.email}
```

***

#<a name=21>Betting CoreOS on Go and Winning</a>
Kelsey Hightower

####CoreOS
- turn white-papers into source code and source code into products (infrastructure company)
- build and ship google-style infrastructure (high scale, etc.) to the masses (https://coreos.com)
- backend written in Go (C when necessary)
- AngularJS for front-end apps
- developer backgrounds
  - no factory methods, bitmasks for function arguments (linux kernel, distributed databases, low level/high performance)
  - community: core maintainers, external contributors, code quality and eng standards
    - drives the ecosystem (std lib, 3rd party pkgs, pkgs and distribution, tools)

####Go @ CoreOS:
- 80K lines of Go, 100s lines of C/Javascript
- Mostly Go std library, Travis CI, Semaphore, jenkins, and testing package, Git
- Statically linked binaries, build and test scripts with go tool
- Vendoring: Goedp (rewrite import paths), GB is awesome but things are fragmented
  - solution for vendoring in Go 1.5
  - Godep stops working if someone deletes dependency repo
- CoreOS Journal

####CoreOS Projects
- CoreOS Linux
- etcd: key value store for shraed config and service discovery
- flannel: etcd-backed network fabric for containers, make nodes have consistence view of cluster
- fleet
- rkt
- Tectonic: turn projects into products
- Kubernetes: manage cluster of machines as a single sysmte to accelerate Dev and simplify Ops
  - google-style framework for application management
  - backed by etcd
  - manage Linux containers (docker, rkt)
  - self-healing infrastructure
  - pods: units of work that hold containers, able to track pods and nodes, move application if node dies
  - scale command => create replicas

####Go Community
- Go community and ecosystem is strong (Canonical, Cloud Flare, Docker, Google, Sound Cloud, Hashicorp, etc.)
- Idea that new infrastructure tools should be written in Go

####Docker
- Linux container runtime engine
- redefined application packaging and deployment
- took OS virtualization mainstream (containers)

***

#<a name=22>Closing Keynote: How Go was Made</a>
Andrew Gerrand

####The Community
- Needs diversity for strength and longevity
- Code of Conduct: specifies standard of respectful behavior, resolving conflicts
  - vital part to bring people into Go

####History: Early Days to Today
- The Early Days
  - Three friends: Griesemer, Pike, Thompson
  - Wanted modern, practical PL
  - Consensus-driven design process, most changes were rejected
  - First Go program was a Prime Sieve
  - Subversion first version control system w/no code review 
    - committed spec, Go compiler, a few pkgs, some test programs
- 2008
  - migrate from Subversion to Perforce, code review platform at Google
  - joined by Russ Cox, Taylor, ~20 Googlers used 20% time
  - easy to work together bc small team, only one repo, easy to make changes
  - waiting for good designs => wait until find the correct design 
    - e.g. reflection capabilities, slices
- 2009: Open Source release, moved to Mercurial with Rietveld for code review
  - mature spec and compiler, broad std lib, gofmt, godoc, cgo, testing framework, FAQ/Effective Go, etc.
  - team was overwhelmed, people sent changes from day 1 => 32 people contributed in first month
  - ideas fragile in early stages, need to be nurtured before exposed to OS
- Changes after the release
  - harder w/OS community, had to send change proposals (design docs)
  - Curating change: many proposed changes and additions, most declined
  - Communicating Goals: poor at first/caused frustration, articulate goals in *Go at Google; Language Design in the Service of SE*
- Managing Change
  - early on weekly snapshots (apply Mercurial tag to specific, stable version)
  - 2011: formal release process, introduce releases every 1-2 months w/abridged "must read" release notes
    - creation of Gofix => eases burden of staying current, works on AST
    - versioning issues persist
      - code you write may not compile tomorrow
      - skew, users could be months behind
- Go 1: Decision to design and implement a stable version of Go, libraries, and tools
  - spec of language and lib supported for years, can download as binary pkgs
  - created go toolset, Error type
  - took time to polish and refine language => bug fixes and TODOs, stabalizing
  - create detailed design document and sent to community
  - users appreciated stability, contributors focused on implementation, tools, ecosystem, not new features
- Release cycle: Go 1.1, switched to 6-month cycle (1.2, 1.3, 1.4, (8 months) 1.5)

####The Future
- Most change proposals made by the Go team at Google
- Contribution guidelines => need better documented process, change proposal process
  - make process more accessible, clear to contributors
  - timely eval of proposals and record history of proposals

####Conclusion
- As Go is more widely used, the community needs a larger role in shaping its future
- Go's design driven process has served us well, will continue to improve with process specs