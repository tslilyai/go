#Index
- [Benchmarking](#bench)
- [Profiling](#profile)

***

#<a name=bench>Benchmarking</a>
`go test -bench`

Write benchmarks in your testing files as follows:

```go
func BenchmarkName(b *testing.B) {
    // do initialization
    b.ResetTimer()

    // b.N determines (based on architecture)
    // how many loops need to be run
    for i := 0; i < b.N; i++ {
        // run code to benchmark
    }
}
```

Other interesting tools:
- [`benchcmp`](http://godoc.org/github.com/Go-zh/tools/cmd/benchcmp): identify performance regressions, compares output from two go benchmark runs
- [`benchstat`](https://godoc.org/rsc.io/benchstat): replacement for `benchcmp` that gives statistically more meaningful output

***

#<a name=profile>Profiling</a>
Using the `test` tool:
- `go test -bench . -cpuprofile=cpu.out`
- `go test -bench . -memprofile=mem.out`

Using the [`pprof`](https://golang.org/pkg/net/http/pprof/) tool:
- `go tool pprof <testbinary> <profile.out>`
- See other uses of pprof (including html output) [here](http://blog.golang.org/profiling-go-programs)
