# RTIC

Real-Time Interrupt-driven Concurrency  
A concurrency framework for building real-time systems

## App

```rust
#[rtic::app(device = lm3s6965, dispatchers = [SSI0, QEI0])]
```

where dispatchers -  [SSI0, QEI0] - are interrupts wich not used in current application, so RTIC used avalible interrupts 

## Idle

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