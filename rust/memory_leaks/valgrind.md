# valgrind

Provides check for memory errors

## installing and ussage

```bash
sudo apt install valgrind

cargo run app_name && valgrind ./target/debug/app_name
```

### result example

```console
    Finished dev [unoptimized + debuginfo] target(s) in 0.03s
     Running `target/debug/add_phantom`
two feet: Length(24.0, PhantomData<add_phantom::Inch>)
two meters: Length(2000.0, PhantomData<add_phantom::Mm>)
==22658== Memcheck, a memory error detector
==22658== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.
==22658== Using Valgrind-3.16.1 and LibVEX; rerun with -h for copyright info
==22658== Command: ./target/debug/add_phantom
==22658== 
two feet: Length(24.0, PhantomData<add_phantom::Inch>)
two meters: Length(2000.0, PhantomData<add_phantom::Mm>)
==22658== 
==22658== HEAP SUMMARY:
==22658==     in use at exit: 0 bytes in 0 blocks
==22658==   total heap usage: 9 allocs, 9 frees, 2,997 bytes allocated
==22658== 
==22658== All heap blocks were freed -- no leaks are possible
==22658== 
==22658== For lists of detected and suppressed errors, rerun with: -s
==22658== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```
