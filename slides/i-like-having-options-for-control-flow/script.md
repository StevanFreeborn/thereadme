# Video Script: Why I Love Control Flow in C#

---

### The Hook

**[VISUAL: Face Camera]**

"If you hang around .NET social circles long enough, you eventually stumble into the 'Error Handling War'."

"It's usually a false dichotomy between two entrenched camps."

"On one side, you have **Team 'Never Throw'**. They scream that exceptions are expensive! They want you to treat errors as values—like Go or Rust—and use `Result<T>` for everything."

"On the other side, you have **Team 'Always Throw'**. They argue that Result objects are noisy and pollute your method signatures. They say: 'Just throw the exception and let the middleware handle it! Keep the code clean!'"

"But here is the thing: **Why not both?**"

"C# is unique because it allows us to mix paradigms comfortably. Today, I want to show you the 'Sweet Spot' architecture that uses exceptions where they make sense, and results where they belong."

---

### The Architecture Overview

**[VISUAL: Slide 3]**

"I structure my Web APIs using a gradient of control flow. As you move up the stack—from the database to the API endpoint—the strategy shifts."

"It looks like this:"

**[CLICK]**

"Deep in the stack, in the Domain and Infrastructure, we **Throw Exceptions**. The context is unknown, and failures here are usually unrecoverable."

**[CLICK]**

"In the Service Layer, we catch those exceptions and **Return Results**. This is the boundary where we translate chaos into order."

**[CLICK]**

"In the Controller, we **Match Results**. We map the result state directly to HTTP status codes."

**[CLICK]**

"And finally, at the top, a **Global Handler** catches the crashes we didn't plan for."

---

### Layer 1: Deep in the Stack

**[VISUAL: Slide 4]**

"Let's look at the code. Starting deep in the stack."

"When I am writing infrastructure code or core domain logic, I don't want to return `Result<User>`. That code doesn't know it's part of an HTTP request. It doesn't know about `404 Not Found`."

"If the database is down, or a business invariant is violated—like a user being banned—I want to throw. This is an exceptional state for this specific logic."

---

### Layer 2: The Service Layer

**[VISUAL: Slide 5]**

"This is where the magic happens. The Service Layer acts as the translator."

"It calls that 'dangerous' deep code, but it wraps it in a `try/catch` block."

"We catch the *specific* exceptions we expect—like `UserBannedException`—and convert them into explicit `Result` failures."

"This keeps the exception 'blast radius' contained. The Service Layer is saying: 'I know this might fail, and I am making that failure part of the contract.'"

---

### Layer 3: The Controller

**[VISUAL: Slide 6]**

"By the time we reach the Controller, `try/catch` blocks are usually a 'code smell'. The Controller shouldn't be worrying about stack traces; it should be worrying about HTTP."

"Because our Service returned a `Result`, we can use C#'s pattern matching."

"Look how clean this is. If it's a success? Return `200 OK`. If it's `NotFound`? Return `404`. If it's `Banned`? Return `403`. It reads like a list of instructions."

---

### Layer 4: The Safety Net

**[VISUAL: Slide 7]**

"Finally, what about the truly unexpected? The Null References, the Out of Memory errors?"

"That is what the Global Exception Handler is for. It sits at the very top of the pipeline."

"If an exception gets past the Service Layer, it means we didn't plan for it. So we log it as an error, and we return a generic 500. We don't try to be clever here; we just try not to crash."

---

### Conclusion

**[VISUAL: Slide 8]**

"This is why I love C#. It doesn't force you into one box."

"You don't have to pick a team. You don't have to be 'Team Throw' or 'Team Result'."

**[CLICK]**
"Use exceptions for errors that occur deep in the stack where context is limited."

**[CLICK]**
"Use results when failure is a valid business outcome."

**[CLICK]**
"And use global handlers to catch the things you didn't see coming."

"Thanks for watching."
