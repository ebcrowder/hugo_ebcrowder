---
date: "2020-04-13"
title: "pargs"
tags: ["Rust"]
draft: false
---

# Building `rust_ledger`

After building a few trivial things in Rust, the first sizeable application that I built was an accounting tool called [rust_ledger](https://crates.io/crates/rust_ledger). I thought it would be a fun way to learn how to build a command line application in a domain that I was very familiar with. I took a lot of inspiration from [ledger-cli](https://www.ledger-cli.org/), which is a very simple and powerful accounting system that operates on plain-text files.

# Parsing arguments is difficult

During the course of building `rust_ledger`, I rolled my own logic for parsing command line arguments. Although `rust_ledger` only had 4 different types of commands, the logic quickly became a tangled mess that was full of bugs and hard to make sense of. I eventually found a solution and shipped `rust_ledger` but was never really satisfied with the result.

There are a lot of [crates](https://lib.rs/search?q=command+line+arguments) that handle command line arguments for CLI programs, but it occurred to me that it would be a fun challenge to try to create my own command line parsing library. It provided me with an opportunity to learn more about Rust's ecosystem and it taught me a thing or two about command line software. Thankfully, Rust's `cargo` [documentation](https://doc.rust-lang.org/cargo/) is very helpful and made the process of learning how to build a library simple and fun.

# What does `pargs` do?

`pargs` simply takes a `Vec<String>` of command line arguments as input and matches them with three `Vec<String>` lists of expected arguments to be consumed by the program. Successfully matched arguments are returned to the program in a `Matches` struct for easy parsing and lookup. Most command line parsing libraries in the Rust ecosystem have more useful functionality (validation, for example), but I purposely wanted a limited API for maximum composability and utility. Keeping the feature set limited allows me to achieve this.

# Challenges and wins

So far, I have changed the API at least 3 times. Handling `option_args`, which could either be denoted via flag and a value separated by a space (ie `-f` `value`) or via assignment (`-f=value`). I eventually settled on using `HashMap` as it made the most sense to store these values as key value pairs for easy lookup.

I was able to test the usefulness of the library by implementing it in `rust_ledger` with some success. It made my argument parsing logic a lot easier to understand and less error prone. I look forward to using it in future projects and iterating further. In the meantime, I hope it is useful for other folks to use.

# Give it a try!

The code can be found [here](https://github.com/ebcrowder/pargs). I am definitely looking for feedback, so let me know if you find it useful!
