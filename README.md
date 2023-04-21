# notes

### [Rust](https://github.com/a-givertzman/notes/tree/master/rust)

#### [Rust embedded](https://github.com/a-givertzman/notes/tree/master/rust/embedded)
  - **stm32**
    * [ARM Cortex-M]
    * [ARM Cortex-M Instruction sets]
    * Build (cross compilation) 
        [chech github for detales](https://github.com/rust-embedded/cortex-m-quickstart)  
        .cargo/config:
```
[build]
# Pick ONE of these compilation targets
# target = "thumbv6m-none-eabi"    # Cortex-M0 and Cortex-M0+
# target = "thumbv7m-none-eabi"    # Cortex-M3
# target = "thumbv7em-none-eabi"   # Cortex-M4 and Cortex-M7 (no FPU)
target = "thumbv7em-none-eabihf" # Cortex-M4F and Cortex-M7F (with FPU)
# target = "thumbv8m.base-none-eabi"   # Cortex-M23
# target = "thumbv8m.main-none-eabi"   # Cortex-M33 (no FPU)
# target = "thumbv8m.main-none-eabihf" # Cortex-M33 (with FPU)
```
    - possible targets for crosscompilation:  
      to list all supported targets use: ```rustup target list```
          thumbv6m-none-eabi  
          thumbv7em-none-eabi  
          thumbv7em-none-eabihf  
          thumbv7m-none-eabi  
          thumbv7neon-linux-androideabi  
          thumbv7neon-unknown-linux-gnueabihf  
          thumbv8m.base-none-eabi  
          thumbv8m.main-none-eabi  
          thumbv8m.main-none-eabihf  
          *thumb - name of the instructions*
          *v6m, v7m/em, v8m - [arm core architecture](https://en.wikipedia.org/wiki/ARM_architecture_family)*  
          *eabi - embedded application binary interface*
          *hf - hard floating (FPU operations will performed (faster) on the hardware level)*
          *sf - soft floating (FPU operations will performed (slower) on the software level)*


  - **esp32**





[ARM Cortex-M]: https://en.wikipedia.org/wiki/ARM_Cortex-M
[ARM Cortex-M Instruction sets]: https://en.wikipedia.org/wiki/ARM_Cortex-M#Instruction_sets
