# RTIC

Real-Time Interrupt-driven Concurrency
A concurrency framework for building real-time systems

```rust
#[idle]
fn idle(_:idle::Context) -> {
    loop {
        // to do nothing: 
        rtic::export::nop()
        // to sleep: 
        rtic::export::wfi()
    }
}
```


## references

- [book on rtic.rs](https://rtic.rs/1/book/en/preface.html)