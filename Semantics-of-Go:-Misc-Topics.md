(From Gophercon 2015 Workshops)

##Internal Packages:
- code too big for a single package, but need to export things
- allows internal code to be factored into packages without changing public API
  - export things but contain within project
  - `/a/internal/x` can be imported by code rooted in `/a` but not by code rooted in `/b`

##Remote/Custom Import Paths
- repos can be hosted on arbitrary servers using meta tags
- see golang.org/cmd/go/#hdr-Remote_import_paths and golang.org/s/go14customimport

```<meta name="go-import" content="rsc.io/pdf git https://github.com/rsc/pdf">```

- packages available from multiple import paths restricted to single path in package declaration

```package pdf // import "rsc.io/pdf"```

##Cross compiling
- see golang.org/pkg/go/build/ and tip.golang.org/doc/install/source#environment
- compiler doesn’t care what build platform is, only what target platform is (every compilation is a cross-compilation)
- Go 1.5 has cross-compilation capabilities built-in (not separate for each target arch)
- environment variables control build targets

##Paths
`import/path/` means `import/path` and all child paths

##Build Tags:
- source files conditionally built based on build tags (booleans)
- GOOS, GOARCH tags set implicitly
- special filenames match build target tags (avoid underscores! treated specially)
  - `name_darwin_amd64.go` builds only for 64-bit Intel Macs
  - `name_openbsd.go` builds for OpenBSD targets, regardless of architecture
  - `name_386.go` builds for x86 targets, regardless of OS
Inside go file:

```go
// +build linux,386 darwin,!cgo
// +build customtag

// Any comments right above package will be seen as documentation!
package xyz
```

The above file will build only if: `((linux && 386) || (darwin && !cgo)) && customtag`

```go build -tags customtag```

##Shared Libraries and Execution Modes
- Go 1.5/1.6, golang.org/s/execmodes
- “plugins” loaded by Go
- bundle code into shared libraries loaded by Go/non-Go, position-independent executables