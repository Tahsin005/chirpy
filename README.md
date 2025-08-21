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

## Lession 3 (workflow tips)

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