**If you should need to debug your programs, see [Go Debugging Tools](https://github.com/thumbtack/wiki/wiki/Tools-in-Go#1)**

#<a name=test>Testing</a>
**You should test your service using the following 4 methods**

1. [Unit Tests](#unit)
2. [Integration Tests](#integration)
3. [Load/Validation Tests](#load)
4. [Static Analysis](#static)

***

##<a name="unit">1. Unit Tests</a>
**Write unit tests for every change you make**. Try to test every line, but there will be lines that just aren't worth covering. This is a snapshot of the current coverage the Agonas service has:

![Current Coverage](http://i.imgur.com/C7aCy5F.png)

### Writing Tests

Unit tests should be written in separate files named `filename_test.go` (i.e. code in `handler.go` would be tested in file `handler_test.go`), which ensures these files are not compiled. Placing your test file in a separate pkg will allow for "black-box testing", i.e. testing only the exported functions/variables of the tested file. If you require testing of private features/functions, place the test file in the same pkg as your code.

See the [Go testing pkg](http://golang.org/pkg/testing/) for more examples and information and on how to properly name [benchmarking](https://github.com/thumbtack/wiki/wiki/Benchmarking-and-Profiling-in-Go) and godoc [example](https://blog.golang.org/examples) functions.  In addition, you can add subtests for each function via Golang's testing naming scheme.

It's the Golang way to write [table driven tests](https://github.com/golang/go/wiki/TableDrivenTests). This style of tests makes it easy to cover a lot of the different permutations of parameters.

We also use `testify/mock` to create mocks of different dependencies. If you have a third party library that doesn't provide an interface to mock, wrap the library in an interface, and then mock the crap out of it. See an example [here](https://github.com/thumbtack/kiki/blob/master/senders/testutils.go). The `testify` github repo also has several other useful testing pkgs, such as `assert` and `require`, which you might want to check out.

### Running Tests
`make test` will test all packages with coverage and verbosity turned on.

Alternatively, run `go test ./... -cover` for a less verbose version with package by package test coverage.

### Checking Coverage

To see line by line coverage of a package, change directory into the package folder, and run `go test -coverprofile=coverage.out; go tool cover -html=coverage.out`.

### Tests on CI

We use [Travis](https://magnum.travis-ci.com/thumbtack/agonas) to automatically build and test on pushes to Github. If your tests succeed on `master`, Travis will automatically trigger a deploy to staging. In other words, a push to `origin/master` is equivalent to a deploy to staging.

***

##<a name=integration>2. Integration Tests</a>

***

##<a name=load>3. Load/Validation Tests</a>

***

##<a name=static>4. Static Analysis</a>
See `static analysis tools`[

Both of these are run by `make test`, and must pass in order to deploy.