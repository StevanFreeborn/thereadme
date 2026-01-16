# Script: A Trick for Designing Friendly APIs in Go

---

### The Hook (The Pain)

**[VISUAL: Face Camera]**

"There is nothing worse than opening a codebase, finding a constructor, and seeing a list of magic numbers and boolean flags."

"You know the ones. `NewServer(true, 30, false)`."

"What is true? What is 30? Is false good or bad? You have to go read the definition just to understand the call site. It’s a bad developer experience."

---

### The Problem

**[VISUAL: Slide 2 - The Optional Configuration Problem]**

"This is the problem we are trying to solve. The 'Optional Configuration' problem."

"Go doesn't support method overloading like C# or Java. So when you write a constructor like `NewServer`, you’re stuck with that signature."

"If you have optional parameters—like a timeout or a verbose flag—you end up forcing the user to pass `0` or `nil` or `true` just to satisfy the compiler."

---

### The Breaking Change

**[VISUAL: Slide 3 - "We need to add a TLS parameter"]**

"And it gets worse when requirements change. Let's say six months later, you need to add TLS support."

"If you just add a parameter to the function, you have now broken every single place in your application where `NewServer` was called. You have to go refactor the entire world just to add one optional setting."

---

### The "Okay" Alternative

**[VISUAL: Slide 4 - "Just use a struct"]**

"Now, the common response is: 'Just use a config struct.'"

"And honestly? This is fine. It's better than magic numbers. But it has a flaw: Ambiguity around zero values."

"If I pass `Port: 0` in that config... do I mean 'Please bind to a random available port'? Or do I mean 'I forgot to set this, please use the default port 80'?"

"Distinguishing between 'default' and 'intentionally zero' is annoying with structs."

---

### The Solution

**[VISUAL: Slide 5 - "Nah bro, functional options"]**

"So, the Go community settled on a better pattern. Functional Options."

"It looks a little scary at first because it uses closures, but once you get it, you’ll never go back."

---

### Step 1: The Type

**[VISUAL: Slide 6 - 1. Define the option]**

"Step one: We define our Server struct, but we keep the fields unexported."

"Then, we define a type—let's call it `Option`. An `Option` is just a function that takes a pointer to your Server and modifies it. It returns nothing."

---

### Step 2: The Closures

**[VISUAL: Slide 7 - 2. Create closures]**

"Step two: We create 'Constructor' functions that return that `Option` type."

"This is where the magic happens. Look at `WithPort`. It takes an integer, and it returns a closure that *captures* that integer."

"When that inner function eventually runs, it will apply that specific port to the server."

---

### 2:45 - Step 3: The Variadic Constructor

**[VISUAL: Slide 8 - 3. Get variadic]**

"Step three: The Constructor."

"Instead of taking a fixed list of arguments, we take a variadic slice of our `Option` type."

**[CLICK to reveal the implementation]**

"Inside the constructor, we do three things:"
1.  "We initialize the server with sensible **defaults**. (Host is localhost, Port is 80)."
2.  "We loop through the provided options and execute them. This overwrites the defaults with whatever the user provided."
3.  "We return the server."

---

### The Result

**[VISUAL: Slide 9 - Almost as good as C#]**

"The result is a beautiful API usage."

"For my C# friends out there, this feels a lot like Named Parameters. It reads like a sentence: `NewServer, WithHost, WithPort`."

"I don't need to know what the default timeout is. I don't need to pass `nil` for things I don't care about. I just declare what I want to change."

---

### Extensibility

**[VISUAL: Slide 10 - Extensibility? We got you.]**

"But the killer feature is extensibility."

**[CLICK to reveal WithTLS]**

"Let's go back to that TLS requirement. I can add a `WithTLS` option function..."

**[CLICK to reveal usage]**

"...and I don't break a single existing line of code. `srv` (the first one) still compiles perfectly. `srv2` uses the new feature. Backward compatibility is preserved."

---

### Summary

**[VISUAL: Slide 11 - So why use this pattern?]**

"To recap, why should you use this?"

**[CLICK]** "1. Defaults are easy. You define them once in the constructor, and the user never has to think about them."
**[CLICK]** "2. It's self-documenting. No more `true, false, 30`."
**[CLICK]** "3. It's future-proof. You can add new options forever without breaking your consumers."

---

### Outro

**[VISUAL: Slide 12 - Go forth...]**

"This is the standard for a reason. If you're building a library or a package in Go, treat your users nicely. Use Functional Options."

"I'll see you in the next one."
