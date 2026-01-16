---
theme: default
layout: center
class: text-center
fonts:
  sans: "CaskaydiaCove Nerd Font Mono"
---

# A Trick for Designing<br>Friendly APIs in Go

---
layout: center
---

# The Optional Configuration Problem

```go
// No defaults?
// What is sensible?
// What does 'true' do?
// What is '30'?
srv := NewServer("localhost", 8080, 30, true)
```

---
layout: center 
---

# "We need to add a TLS parameter."

```go
func NewServer(host string, port int, timeout int) *Server {
    // ...
}
```

---
layout: center 
---

# "Just use a struct"

```go
srv := NewServer(Config{
    Host: "localhost",
    Port: 0, 
})
```

---
layout: center 
class: text-center
---

# "Nah bro, use functional options."

---
layout: center
---

# 1. Define the option

```go
type Server struct {
    host string
    port int
}

// Option is a function that modifies the Server
type Option func(*Server)
```

---
layout: center
---

# 2. Create closures

```go
// WithPort captures the 'port' value
func WithPort(port int) Option {
    // It returns a closure
    return func(s *Server) {
        s.port = port
    }
}

func WithHost(h string) Option {
    return func(s *Server) {
        s.host = h
    }
}
```

---
layout: center 
class: text-center
---

# 3. Get variadic

<div class="grid grid-cols-2 gap-10 text-left mt-10">
<div>
```go
func NewServer(host string, port int) *Server {
    return &Server{
        host: host,
        port: port,
    }
}
```
</div>
<div>
<v-click>
```go
func NewServer(opts ...Option) *Server {
    
    s := &Server{
        host: "localhost", 
        port: 80,
    }

    for _, opt := range opts {
        opt(s)
    }

    return s
}
```
</v-click>
</div>
</div>

---
layout: center 
---

# Almost as good as C#

```go
srv := NewServer(
    WithHost("api.google.com"),
    WithPort(9090),
    WithTimeout(30 * time.Second),
)
```

---
layout: center
class: text-center
---

# Extensibility? We got you.

<div class="grid grid-cols-2 gap-10 text-left mt-10">
<div>
<v-click>
Support TLS...
```go
func WithTLS(cert, key string) Option {
    return func(s *Server) {
        s.tls = true
        s.cert = cert
        s.key = key
    }
}
```
</v-click>
</div>
<div>
<v-click>
Breaks nothing.
```go
srv := NewServer(WithPort(8080))

srv2 := NewServer(
    WithPort(8080),
    WithTLS("cert.pem", "key.pem"),
)
```
</v-click>
</div>
</div>

---
layout: center 
class: text-center
---

# So why use this pattern?

<div class="text-left mx-auto w-max text-xl leading-10 flex flex-col">
    <div>
        <v-click>
            <carbon:checkmark-filled class="text-green-500 mr-2"/> Defaults are easy (just call New)
        </v-click>
    </div>
    <div>
        <v-click>
            <carbon:checkmark-filled class="text-green-500 mr-2"/> Usage is self-documenting
        </v-click>
    </div>
    <div>
        <v-click>
            <carbon:checkmark-filled class="text-green-500 mr-2"/> New options don't break old code
        </v-click>
    </div>
</div>

---
layout: center
class: text-center
---

# <span class="text-[#00ADD8]">Go</span> forth and build friendly APIs!

