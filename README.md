# chirpy

```
Default Base URL: http://localhost:8080
Optionally configure your CLI to override the default base URL by running bootdev config base_url <url>
```

### Lesson 1 (server)

```
bootdev run 861ada77-c583-42c8-a265-657f2c453103
bootdev run 861ada77-c583-42c8-a265-657f2c453103 -s
```

## Lesson 2 (fileserver)

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

## Lession 11 (architecture - monoliths and decoupling)

```
A monolith is a single, large program that contains all of the functionality for both the front-end and the back-end of an application. It's a common architecture for web applications.

A "decoupled" architecture is one where the front-end and back-end are separated into different codebases. For example, the front-end might be hosted by a static file server on one domain, and the back-end might be hosted on a subdomain by a different server.

Which Is Better?

There is always a trade-off.
Pros for Monoliths

    Simpler to get started with
    Easier to deploy new versions because everything is always in sync
    In the case of the data being embedded in the HTML, the performance can result in better UX and SEO


Pros for Decoupled Architectures

    Easier, and probably cheaper, to scale as traffic grows
    Easier to practice good separation of concerns as the codebase grows
    Can be hosted on separate servers and using separate technologies
    Embedding data in the HTML is still possible with pre-rendering (similar to how Next.js works), it's just more complicated

Which will probably be cheaper to host as traffic and scope increases?
- Decoupled
```

## Lession 12 (architecture - admin namespace)

```
"admin" namespace. This is where we'll put endpoints intended for internal administrative use. Note: there's nothing inherently more secure about this namespace, it's just an organizational structure.

bootdev run 892b38f7-d154-4591-ac63-a9fbc2a38187
bootdev run 892b38f7-d154-4591-ac63-a9fbc2a38187 -s
```

## Lession 13 (architecture - deployment options)

```
ow our choice of project architecture affects our deployment options, and how we could deploy our application in the future. We'll only talk about cloud deployment options here, and by the "cloud" I'm just referring to a remote server that's managed by a third-party company like Google or Amazon.


Monolithic Deployment

Deploying a monolith is straightforward. Because your server is just one program, you just need to get it running on a server that's exposed to the internet and point your DNS records to it.

You could upload and run it on classic server, something like:

    AWS EC2
    GCP Compute Engine (GCE)
    Digital Ocean Droplets
    Azure Virtual Machines

Alternatively, you could use a platform that's specifically designed to run web applications, like:

    Heroku
    Google App Engine
    Fly.io
    AWS Elastic Beanstalk

Decoupled Deployment

With a decoupled architecture, you have two different programs that need to be deployed. You would typically deploy your back-end to the same kinds of places you would deploy a monolith.

For your front-end server, you can do the same, or you can use a platform that's specifically designed to host static files and server-side rendered front-end apps, something like:

    Vercel
    Netlify
    GitHub Pages

A classic virtual server like EC2 or GCE can be used to host...
- Both

Which is a better fit for Netlify?
- Static front-end asset server
```

## Lesson 14 (json - json)

```
Decode JSON Request Body

It's very common for POST requests to send JSON data in the request body. Here's how you can handle that incoming data:

{
  "name": "John",
  "age": 30
}

func handler(w http.ResponseWriter, r *http.Request){
    type parameters struct {
        // these tags indicate how the keys in the JSON should be mapped to the struct fields
        // the struct fields must be exported (start with a capital letter) if you want them parsed
        Name string `json:"name"`
        Age int `json:"age"`
    }

    decoder := json.NewDecoder(r.Body)
    params := parameters{}
    err := decoder.Decode(&params)
    if err != nil {
        // an error will be thrown if the JSON is invalid or has the wrong types
        // any missing fields will simply have their values in the struct set to their zero value
		log.Printf("Error decoding parameters: %s", err)
		w.WriteHeader(500)
		return
    }
    // params is a struct with data populated successfully
    // ...
}


Encode JSON Response Body

func handler(w http.ResponseWriter, r *http.Request){
    // ...

    type returnVals struct {
        // the key will be the name of struct field unless you give it an explicit JSON tag
        CreatedAt time.Time `json:"created_at"`
        ID int `json:"id"`
    }
    respBody := returnVals{
        CreatedAt: time.Now(),
        ID: 123,
    }
    data, err := json.Marshal(respBody)
	if err != nil {
		log.Printf("Error marshalling JSON: %s", err)
		w.WriteHeader(500)
		return
	}
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(200)
    w.Write(data)
}
```

## Lesson 15 (json - json review)

```
struct - 1
type parameters struct {
    Name string `json:"name"`
    Age int `json:"age"`
    School struct {
        Name string `json:"name"`
        Location string `json:"location"`
    } `json:"school"`
}

struct - 2
type parameters struct {
    name string `json:"name"`
    Age int `json:"age"`
}

struct - 3
type parameters struct {
    Name string
    Age int
}

What are the keys in the JSON representation of struct 1?
- name, age, school, school.name, school.location

Which keys will be parsed in the JSON representation of struct 2?
- name, age

What are the keys in the JSON representation of struct 3?
- Name, Age
```

## Lesson 16 (storage - storage)

```
Storage

Arguably the most important part of your typical web application is the storage of data. It would be pretty useless if each time you logged into your account on YouTube, Twitter or GitHub, all of your subscriptions, tweets, or repositories were gone.


Memory vs. Disk

When you run a program on your computer (like our HTTP server), the program is loaded into memory. Memory is a lot like a scratch pad. It's fast, but it's not permanent. If the program terminates or restarts, the data in memory is lost.

When you're building a web server, any data you store in memory (in your program's variables) is lost when the server is restarted. Any important data needs to be saved to disk via the file system.


Option 1: Raw Files

We could take our user's data, serialize it to JSON, and save it to disk in .json files (or any other format for that matter). It's simple, and will even work for small applications. Trouble is, it will run into problems fast:

    Concurrency: If two requests try to write to the same file at the same time, you'll get overwritten data.
    Scalability: It's not efficient to read and write large files to disk for every request.
    Complexity: You'll have to write a lot of code to manage the files, and the chances of bugs are high.


Option 2: a Database

At the end of the day, a database technology like MySQL, PostgreSQL, or MongoDB "just" writes files to disk. The difference is that they also come with all the fancy code and algorithms that make managing those files efficient and safe. In the case of a SQL database, the files are abstracted away from us entirely. You just write SQL queries and let the DB handle the rest.
```

```
[~] tahsin005  sudo service postgresql start

[~] tahsin005  sudo -u postgres psql
psql (16.9 (Ubuntu 16.9-0ubuntu0.24.04.1))
Type "help" for help.

postgres=# CREATE DATABASE chirpy;
CREATE DATABASE
postgres=# \c chirpy
You are now connected to database "chirpy" as user "postgres".
chirpy=# ALTER USER postgres WITH PASSWORD 'postgres';
ALTER ROLE
chirpy=# SELECT version();
chirpy=#
```

## Lesson 17 (storage - goose migrations)

```
Goose Migrations

Goose is a database migration tool written in Go (CLI Tool). It runs migrations from a set of SQL files.


What Is a Migration?

A migration is just a set of changes to your database table. You can have as many migrations as needed as your requirements change over time. For example, one migration might create a new table, one might delete a column, and one might add 2 new columns.

An "up" migration moves the state of the database from its current schema to the schema that you want. So, to get a "blank" database to the state it needs to be ready to run your application, you run all the "up" migrations.

If something breaks, you can run one of the "down" migrations to revert the database to a previous state. "Down" migrations are also used if you need to reset a local testing database to a known state.

A "migration" in Goose is just a .sql file with some SQL queries and some special comments.

[~] tahsin005  main -  psql "postgres://postgres:postgres@localhost:5432/chirpy"

goose postgres <connection_string> up

psql -h localhost -U postgres -d chirpy
\dt
```
## Lesson 18 (storage - sqlc)

```
SQLC

SQLC is an amazing Go program that generates Go code from SQL queries. It's not exactly an ORM, but rather a tool that makes working with raw SQL easy and type-safe.

SQLC is just a command line tool, it's not a package that we need to import.

go install github.com/sqlc-dev/sqlc/cmd/sqlc@latest

version: "2"
sql:
  - schema: "sql/schema"
    queries: "sql/queries"
    engine: "postgresql"
    gen:
      go:
        out: "internal/database"

We're telling SQLC to look in the sql/schema directory for our schema structure (which is the same set of files that Goose uses, but sqlc automatically ignores "down" migrations), and in the sql/queries directory for queries. We're also telling it to generate Go code in the internal/database directory.

SQLC Doc: https://docs.sqlc.dev/en/latest/tutorials/getting-started-postgresql.html

Other required moduels:
    go get github.com/google/uuid
    go get github.com/joho/godotenv
```

## Lesson 19 (storage - database review)

```
Popular Databases

    PostgreSQL: A fantastic open-source SQL database.
    MySQL: Another open-source SQL database. Less fantastic IMO.
    MongoDB: A popular open-source NoSQL document database.
    Firebase: A popular cloud-based NoSQL database service.
    SQLite: A popular embedded SQL database.

Why do we care about whether data is stored in memory or on disk?
- Data in memory is lost when the server is restarted, data on disk persists
```

## Lesson 20 (storage - create user)

```
The Context Package

The context package is a part of Go's standard library. It does several things, but the most important thing is that it handles timeouts. All of SQLC's database queries accept a context.Context as their first argument:

    user, err := cfg.db.CreateUser(r.Context(), params.Email)

By passing your handler's http.Request.Context() to the query, the library will automatically cancel the database query if the HTTP request is canceled or times out.

The benefit is that it will save your server from getting bogged down by long-running queries!
```

## Lesson 21 (storage - collections and singletons)

```
REST is a set of guidelines for how to build APIs. It's not a standard, but it's a set of conventions that many people follow. Not all back-end APIs are RESTful, but many are.


Collections and Singletons

In REST, it's conventional to name all of your endpoints after the resource that they represent and for the name to be plural. That's why we use POST /api/chirps to create a new chirp instead of POST /api/chirp.

To get a collection of resources it's conventional to use a GET request to the plural name of the resource. So we are going to use GET /api/chirps to get all of the chirps.

To get a singleton, or a single instance of a resource, it's conventional to use a GET request to the plural name of the resource, followed by the ID of the resource. So we are going to use GET /api/chirps/94b7e44c-3604-42e3-bef7-ebfcc3efff8f to get the chirp with ID 94b7e44c-3604-42e3-bef7-ebfcc3efff8f.

Which is the RESTful way to design an endpoint that returns a single user resource?
- GET /users/{id}

Which is the RESTful way to design an endpoint that returns all users?
- GET /users
```

## Lesson 22 (authentication - authentication with password)

```
Authentication is the process of verifying who a user is. If you don't have a secure authentication system, your back-end systems will be open to attack!

Passwords

Passwords are a common way to authenticate users. You know how they work: When a user signs up for a new account, they choose a password. When they log in, they enter their password again. The server will then compare the password they entered with the password that was stored in the database.

There are 2 really important things to consider when storing passwords:

    Storing passwords in plain text is awful. If someone gets access to your database, they will be able to see all of your users' passwords. If you store passwords in plain text, you are giving away your users' passwords to anyone who gets access to your database.
    Password strength matters. If you allow users to choose weak passwords, they will be more likely to reuse the same password on other websites. If someone gets access to your database, they will be able to log in to your users' other accounts.

Hashing

On the other hand, we will be writing code to store passwords in a way that prevents them from being read by anyone who gets access to your database. This is called hashing. Hashing is a one-way function. It takes a string as input and produces a string as output. The output string is called a hash.
```

## Lesson 23 (authetication - password review)

```
Passwords Should Be Strong

The most important factor for the strength of a password is its entropy. Entropy is a measure of how many possible combinations of characters there are in a string. To put it simply:

    The longer the password the better
    Special characters and capitals should always be allowed
    Special characters and capitals aren't as important as length

Passwords Should Never Be Stored in Plain Text

The most critical thing we can do to protect our users' passwords is to never store them in plain text. We should use cryptographically strong key derivation functions (which are a special class of hash functions) to store passwords in a way that prevents them from being read by anyone who gets access to your database.

Bcrypt is a great choice. SHA-256 and MD5 are not.

Which is a good hash function for passwords?
- Bcrypt

What is the best measure of password strength?
- length
```

## Lesson 24 (authentication - types of authentication)

```
Here are a few of the most common authentication methods:

    Password + ID (username, email, etc.)
    3rd Party Authentication ("Sign in with Google", "Sign in with GitHub", etc)
    Magic Links
    API Keys

1. Password + ID

This is the most common type of authentication that requires a manual login from a user. When users use password managers, it's one of the more secure ways to authenticate users, unfortunately, many users don't, so it's not as secure as it could be.

That said, it's a valid choice.


2. 3rd Party Authentication

3rd party authentication is a way to authenticate users using a service like Google or GitHub (similar to how we do it here on Boot.dev). 3rd party auth is great for user experience because it allows users to use their existing accounts to log in to your app, lowering friction.

It's also nice because you don't need to worry about storing passwords yourself, meaning you can outsource the security of your users' passwords to a company that, hopefully, does a good job.

The only real drawbacks to 3rd party auth is that you're trusting a 3rd party and if your users don't have an account with that 3rd party, they won't be able to log in.


3. Magic Links

Magic links are a way to authenticate users without a password. It relies on the assumption that the user's email is something that they have unique access to.

The webserver sends a link to the user's email and encodes a unique token in that link. When the user clicks the link, the webserver can decode the token and use it to authenticate the user. Eg:

https://example.com/login?token=...


4. API Keys

API keys are a fantastic way to authenticate users and systems programmatically. An API Key is just a long, secure string that uniquely identifies a user or system, and that can't be guessed. Because they're intended to be used in code, they don't need to be memorized and, as such, can be much longer and double as an identifier. An API key might look something like this:

bd_JDS543J3n5NMKspDXNRlowiqw523lKHK32K43kl

Which makes the most sense to authenticate a user of a command line application for secure API access?
- API key
```

## Lesson 25 (authentication - jwt)

```
A JWT is a JSON Web Token. It's a cryptographically signed JSON object that contains information about the user.

When your server issues a JWT to Bob, Bob can use that token to make requests as Bob to your API. Bob won't be able to change the token to make requests as Alice.
```

## Lesson 26 (authentication - authentication with jwt)

```
Authentication With JWTs

Step 1: Login

It would be pretty annoying if you had to enter your username and password every time you wanted to make a request to an API. Instead, after a user enters a username and password, our server should respond with a token (JWT) that's saved in the client's device.
The token remains valid until it expires, at which point the user will need to log in again.

Step 2: Using the Token

When the user wants to make a request to the API, they send the token along with the request in the HTTP headers. The server can then verify that the token is valid, which means the user is who they say they are.
```