#<a name=style>Index</a>
- [Style and formatting](#style)
- [Handling Errors](#errors)
- [Documentation](#docs)
- [Misc Tips and Warnings](#misc)

***

#<a name=style>Style and Formatting</a>
See [Code Review Comments](https://github.com/golang/go/wiki/CodeReviewComments) for common style and convention errors that have popped up during code reviews.

See [[ Vim with Go ]] on how to get vim set up with Go tools that autoformat upon save. Other editors have similar setups that make formatting and style in Go a non-issue.

See the [Style](https://github.com/thumbtack/wiki/wiki/Tools-in-Go#7) and [Static Analysis](https://github.com/thumbtack/wiki/wiki/Tools-in-Go#6) tools to check that your code matches Go standards.

***

#<a name=errors>Handling Go Errors</a>
### Check Every Error
Errors are first class citizens in Go, and should be treated as such. **Check every error, and handle it appropriately**.

### Fail Fast
Fail as early as possible. For instance, the following block executes on start up: 

	connector := db.NewPgConnector()
	// Dummy connect to make sure we've properly connected to PG.
	db, err := connector.Connect()
	if err != nil {
		log.Fatalf("pg: failed to connect: %s", err.Error())
	}
	db.Close() 

Without this check, any PG connection errors would only appear when a request was received.

### Fix the Error
If at all possible, fix the error from ever happening. Agonas does not error often, and we should strive to keep it this way.

### Critical Errors
If an error happens that blocks the Request from being matched, we should just return an error. This error will eventually get bubbled up, and EB will retry this Request later on.

If a request gets retried too many times, it'll get isolated to a dead letter queue.

### Non-Critical Errors
These are errors where a nice to have, but not really needed component fails. It's okay to just **log the error**, but continue processing the request.

For instance, metrics are nice to have, but if we miss one here or there, it's not the end of the world.

### Error Style
- use `fmt.Errorf` instead of `errors.New` when errors require formatting
- use `errors.New` instead of `fmt.Errorf` if errors do not require formatting

***

#<a name=docs>Documentation</a>

Go to [our godoc server](https://godoc.thumbtack.io), search for your package, and add a link to your package documentation in your repo's README.

*Note*: You will have to search for all internal packages/nested packages in your repo in order for them to show up. E.g. searching `github.com/thumbtack/gott` will show 0 packages unless someone has previously searched `github.com/thumbtack/gott/cors` or another internal package.

Documention tools can be found [here](https://github.com/thumbtack/wiki/wiki/Tools-in-Go#2)

***

#<a name=misc>Misc Tips and Warnings</a>
- use `defer` so you don't forget to close open file handlers or response bodies.
  - WARNING: do NOT `defer` within an infinite loop, or forget to `return` from the function in which the `defer` is stated
- use `log.Fatal/Panic` during startup (nothing else to do, really)
- don't use `go run`, use `go build && ./thebinary` because `go run` is meant for single-file scripts