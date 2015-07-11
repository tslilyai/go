#Index
- [`net/http`: Handlers, Servers, and Routers](#1)
- [Middleware](#2)
- [Testing: `httptest`](#3)

(from Gophercon 2015)
***

#<a name=1>[`net/http` package](http://golang.org/pkg/net/http/)</a>
- http.Handler interface with methods Handle, HandleFunc
  - Handle: pass in httpHandler that serves HTTP
  - HandleFunc: pass in function that matches ServeHttp signature
- ResponseWriter interface (exposes ServeHTTP method)
  - anything that serves HTTP can be composed into one big webapp, simply by registering several endpts
- registering handlers: think of as a “mapping” 
- httpRouters
  - ServeMux: matches URL of incoming request against registered patterns, calls appropriate handler
    - doesn’t differentiate between http methods, not great performance, not much built-in parsing
    - passing in nil as ListenAndServe uses DefaultServeMux
  - [httprouter](github.com/julienschmidt/httprouter): alternative router, performant, handles methods and allows params in endpts
    - r.GET("/posts/:id", PostShowHandler)

```go
func PostShowHandler(rw http.ResponseWriter, r *http.Request, p httprouter.Params) {
    id := p.ByName("id")
    fmt.Fprintln(rw, "showing post", id)
}
```

***

#<a name=2>Middleware</a>
- stack http.Handlers on top of each other, run in sequence (string together)
- control how request comes in before hits handlers
- remove redundancies w/in services
  - e.g. if all require an API key (have to lock/check within more than one handler)
  - e.g. calling log when handler is called, or wanting to log without calling the handler
- wrap handlers with another handler that receives response
  - Auth(Users), Auth(Books): Auth is the wrapper, Users/Books are the handlers
- [`negroni` package](https://github.com/codegangsta/negroni): treats handlers like a stack
  - comes with logging/error/recovery/static file serving
  - can have private and public negroni handlers → e.g. if some endpts don’t need Auth

***

#<a name=3>End-to-End Testing</a>
- `httptest.NewServer` (http://golang.org/pkg/net/http/httptest/)
- see example [here](https://github.com/thumbtack/kiki/blob/master/handlers/handlers_test.go)