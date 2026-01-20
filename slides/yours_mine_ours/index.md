---
title: "Yours, Mine, Ours"
theme: default
layout: center
fonts:
  sans: "CaskaydiaCove Nerd Font Mono"
---

# Yours, Mine, Ours

Understanding ownership and borrowing in Rust

---
layout: center
---

# Think about a book

Rust treats data a lot like a physical book.

---
layout: center
---

# Three rules to remember

<ul>
    <v-click>
        <li>
        1. Each value has a single owner.
        </li>
    </v-click>
    <v-click>
        <li>
        2. There can only be one owner at a time.
        </li>
    </v-click>
    <v-click>
        <li>
        3. When the owner goes out of scope the value is dropped.
        </li>
    </v-click>
</ul>

---
layout: center
---

# Sometimes we do want to buy the book

<div class="grid grid-cols-2 gap-10 text-left">
<div>
```rust{|1|2}
let owner_one = String::from("The Rust Book");
let owner_two = owner_one;
```
</div>
    <div class="flex flex-col gap-4">
       <v-click at="1">
            <div>
                <code>owner_one</code> is the owner.
            </div>
        </v-click>
       <v-click at="2">
            <div>
                <code>owner_one</code> transferred ownership to <code>owner_two</code>.
            </div>
            <div>
                <code>owner_one</code> is invalid now.
            </div>
        </v-click>
    </div>
</div>

---
layout: center
---

# Sometimes we don't want to buy the book

<div class="grid grid-cols-2 gap-10 text-left">
<div>
```rust{|1|2|3-4}
let owner = String::from("The Rust Book");
let borrower = &owner;
println!("I borrowed: {} from the owner!", borrower);
println!("The owner still has: {}", owner);
```
</div>
<div>
    <div class="flex flex-col gap-4">
       <v-click at="1">
            <div>
                <code>owner</code> is the owner.
            </div>
        </v-click>
       <v-click at="2">
            <div>
                <code>borrower</code> borrows from <code>owner</code>.
            </div>
        </v-click>
       <v-click at="3">
            <div>
                Both are valid.
            </div>
        </v-click>
    </div>
</div>
</div>

---
layout: center
---

# Sometimes I want to add notes to the book

<div class="grid grid-cols-2 gap-10 text-left">
<div>
```rust{|1|2|3-4}
let mut owner = String::from("The Rust Book");
let borrower = &mut owner;
borrower.push_str(" - with my notes!");
println!("The owner has: {}", owner);
```
</div>
<div>
    <div class="flex flex-col gap-4">
       <v-click at="1">
            <div>
                <code>owner</code> is the owner.
            </div>
        </v-click>
       <v-click at="2">
            <div>
                <code>borrower</code> mutably borrows from <code>owner</code>.
            </div>
        </v-click>
         <v-click at="3">
                <div>
                 The owner is mutated through the borrower.
                </div>
        </v-click>
    </div>
</div>
</div>

---
layout: center
---

# We have to be thoughtful when borrowing

You don't want to change the book while someone else is reading it.

<v-click>
```rust{1-4|6-8|11-13}
// many immutable borrows allowed
let owner = String::from("The Rust Book");
let borrower1 = &owner;
let borrower2 = &owner;

// only one mutable borrow allowed
let mut owner = String::from("The Rust Book");
let borrower = &mut owner;

// mixing mutable and immutable borrows is not allowed
let mut owner = String::from("The Rust Book");
let borrower1 = &owner;
let borrower2 = &mut owner;

```
</v-click>

---
layout: center
---

# So when to do what?

| Question                                 | Answer | Action           | Type   |
| ---------------------------------------- | ------ | ---------------- | ------ |
| Do I need to consume or delete the data? | YES    | Take Ownership   | T      |
| Do I need to edit the data in place?     | YES    | Mutable Borrow   | &mut T |
| Do I just need to read/analyze the data? | YES    | Immutable Borrow | &T     |
| Is the data tiny (Integers, Bools)?      | YES    | Copy             | T      |

---
layout: center
---

# Calculate the length of a string

```rust
fn calculate_length(s: &str) -> usize {
    s.len()
}

let my_string = String::from("Hello, Rust!");
let length = calculate_length(&my_string);
println!("The length of '{}' is {}.", my_string, length);
```

---
layout: center
---

# Add a signature to a document

```rust
fn add_signature(doc: &mut String, signature: &str) {
    doc.push_str(signature);
}

let mut my_document = String::from("This is an important document.\n");
add_signature(&mut my_document, "\nSigned, Rustacean");

println!("{}", my_document);
```

---
layout: center
---

# Create a new book

```rust
struct Book { title: String, author: String }

fn create_book(title: String, author: String) -> Book {
    Book { title, author }
}

let title = String::from("The Rust Book");
let author = String::from("Steve Klabnik");

let my_book = create_book(title, author);

println!("Created book: '{}' by {}", my_book.title, my_book.author);
```

---
layout: center
---

# Doubling a number

```rust
fn double_number(num: i32) -> i32 {
    num * 2
}

let my_number = 10;
let doubled = double_number(my_number);

println!("The double of {} is {}.", my_number, doubled);
```

---
layout: center
---

# Ownership is the price we pay for memory safety without a garbage collector.

