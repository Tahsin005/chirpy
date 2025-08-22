# chirpy

```
Default Base URL: http://localhost:8080
Optionally configure your CLI to override the default base URL by running bootdev config base_url <url>
```

### Lession 1 (server)

```
bootdev run 861ada77-c583-42c8-a265-657f2c453103
bootdev run 861ada77-c583-42c8-a265-657f2c453103 -s
```

## Lession 2 (fileserver)

```
bootdev run 8cf7315a-ffc0-4ce0-b482-5972ab695131
bootdev run 8cf7315a-ffc0-4ce0-b482-5972ab695131 -s
```

```
srv := &http.Server{
    Addr:    ":" + port,
    Handler: mux,
}

log.Printf("Serving files from %s on port: %s\n", filepathRoot, port)
log.Fatal(srv.ListenAndServe())

What is http.Server?
- A struct that describes a server configuration

What happens when ListenAndServe() is called?
- The main function blocks until the server is shut down
```

## Lession 3 (serving images)

```
bootdev run 20709716-4d7c-47fe-b182-9bccf8436ddc
bootdev run 20709716-4d7c-47fe-b182-9bccf8436ddc -s
```

```
When using a standard fileserver, the path to a file on disk is the same as its URL path. An exception is that index.html is served from / instead of /index.html.
```

## Lession 4 (workflow tips)

```
Servers are different. They run forever, waiting for requests to come in, processing them, sending responses, and then waiting for the next request. If they didn't work this way, websites and apps would be down and unavailable all the time!

Debugging a Server:
    The simplest way (minimal tooling) is to:

        Write some code.
        Build and run the code.
        Send a request to the server using a browser or some other HTTP client.
        See if it did what you expected.
        If it didn't, add some logging or fix the code, and go back to step 2.

Restarting a server:
    I usually use a single command to build and run my servers, assuming I'm in my main package directory:
    ```
    go run .
    ```
    This builds the server and runs it in one command.

    To stop the server, I use ctrl+c. This sends a signal to the server, telling it to stop. The server then exits.

    To start it again, I just run the same command.

    Alternatively, you can compile a binary and run it instead
    ```
    go build -o out && ./out
    ```

What does the && do in this command? go build -o out && ./out
- If the go build -o out succeeds with exit code 0, it runs ./out command
```

## Lession 5 (custom handlers)

```
bootdev run 174d13f0-f887-46c6-a633-d963662fde39
bootdev run 174d13f0-f887-46c6-a633-d963662fde39 -s
```

## Lession 6 (handler review)

```
Handler:
An http.Handler is any defined type that implements the set of methods defined by the Handler interface, specifically the ServeHTTP method.
    ```
    type Handler interface {
        ServeHTTP(ResponseWriter, *Request)
    }
    ```
The ServeMux used in the previous exercise is an http.Handler.

You will typically use a Handler for more complex use cases, such as when you want to implement a custom router, middleware, or other custom logic.

HandlerFunc:
    ```
    type HandlerFunc func(ResponseWriter, *Request)
    ```
You'll typically use a HandlerFunc when you want to implement a simple handler. The HandlerFunc type is just a function that matches the ServeHTTP signature above.

From which parameter would you get information about the HTTP method used by the client?
- *Request
```

## Lession 7 (routing - stateful handlers)

```
The atomic.Int32 type is a really cool standard-library type that allows us to safely increment and read an integer value across multiple goroutines (HTTP requests).

The atomic.Int32 type has an .Add() method, use it to safely increment the number of fileserverHits.
```

## Lession 8 (routing - middleware)


```
Middleware

Middleware is a way to wrap a handler with additional functionality. It is a common pattern in web applications that allows us to write DRY code. For example, we can write a middleware that logs every request to the server. We can then wrap our handler with this middleware and every request will be logged without us having to write the logging code in every handler.

Here are examples of the middleware that we've written so far.

Keeping Track of the Number of Times a Handler Has Been Called

func middlewareLog(next http.Handler) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		log.Printf("%s %s", r.Method, r.URL.Path)
		next.ServeHTTP(w, r)
	})
}
```

## Lession 9 (routing - routing)

```
Using the Go standard library, we can specify a method like this: [METHOD ][HOST]/[PATH]. For example:

mux.HandleFunc("POST /articles", handlerArticlesCreate)
mux.HandleFunc("DELETE /articles", handlerArticlesDelete)

bootdev run d1c4962d-f55e-4f0f-ac9c-913db8ef8ae8
bootdev run d1c4962d-f55e-4f0f-ac9c-913db8ef8ae8 -s
```

## Lession 10 (routing - patterns)

```
A pattern is a string that specifies the set of URL paths that should be matched to handle HTTP requests. Go's ServeMux router uses these patterns to dispatch requests to the appropriate handler functions based on the URL path of the request. As we saw in the previous lesson, patterns help organize the handling of different routes efficiently.


Rules and Definitions
    Fixed URL Paths

    A pattern that exactly matches the URL path. For example, if you have a pattern /about, it will match the URL path /about and no other paths.

    Subtree Paths

    If a pattern ends with a slash /, it matches all URL paths that have the same prefix. For example, a pattern /images/ matches /images/, /images/logo.png, and /images/css/style.css. As we saw with our /app/ path, this is useful for serving a directory of static files or for structuring your application into sub-sections.

    Longest Match Wins

    If more than one pattern matches a request path, the longest match is chosen. This allows more specific handlers to override more general ones. For example, if you have patterns / (root) and /images/, and the request path is /images/logo.png, the /images/ handler will be used because it's the longest match.

    Host-Specific Patterns

    We won't be using this but be aware that patterns can also start with a hostname (e.g., www.example.com/). This allows you to serve different content based on the Host header of the request. If both host-specific and non-host-specific patterns match, the host-specific pattern takes precedence.

Given the patterns /assets/ and /assets/css/, which pattern would be selected to handle a request for the URL path /assets/css/style.css?
- 
```