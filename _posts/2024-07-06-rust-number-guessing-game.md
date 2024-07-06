---
layout: post
category: technical
custom_js: mouse_coords
---

## Number Guessing game in Rust

In this post I demonstrate how to create a number guessing game in Rust using match expression as well as without using it


```
use std::io;
use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Guess the number between 1 to 10!");
    let secret_number: u32 = rand::thread_rng().gen_range(1..=10);
    loop {
        println!("Please insert your guess number!");
        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read.");
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };
        println!("You guessed: {}", guess);
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too Small!"),
            Ordering::Greater => println!("Too Large!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

#### Cargo.toml
```
[package]
name = "number_guess"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
rand = "0.8.5"
```

#### Imports
- `rand::Rng`: Imports the `Rng` trait from the `rand` crate, which provides methods for random number generation.
- `std::cmp::Ordering`: Imports the `Ordering` enum, which is used to compare numbers and can be `Less`, `Greater`, or `Equal`.
- `std::io`: Imports the `io` module from the standard library, used for handling input and output.

#### Same program without using match 

```
use rand::Rng;
use std::io;

fn main() {
    println!("Guess the number!");

    let secret_number = rand::thread_rng().gen_range(1..=100);

    loop {
        println!("Please input your guess.");

        let mut guess = String::new();

        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = if let Ok(num) = guess.trim().parse() {
            num
        } else {
            continue;
        };

        println!("You guessed: {guess}");

        if guess < secret_number {
            println!("Too small!");
        } else if guess > secret_number {
            println!("Too big!");
        } else {
            println!("You win!");
            break;
        }
    }
}

```


