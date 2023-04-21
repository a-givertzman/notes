### stm32
* Development documentation:
    [on Arm Developer](https://developer.arm.com/documentation/#&cf[navigationhierarchiesproducts]=%20IP%20Products,Processors,Cortex-M)
* [ARM Cortex-M]
* [ARM Cortex-M Instruction sets]
* Build (cross compilation) 
    [chech github for detales](https://github.com/rust-embedded/cortex-m-quickstart)  
    [.cargo/config:](https://github.com/rust-embedded/cortex-m-quickstart#:~:text=Set%20a%20default%20compilation%20target.%20There%20are%20four%20options%20as%20mentioned%20at%20the%20bottom%20of%20.cargo/config.%20For%20the%20STM32F303VCT6%2C%20which%20has%20a%20Cortex%2DM4F%20core%2C%20we%27ll%20pick%20the%20thumbv7em%2Dnone%2Deabihf%20target.)
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

- or use build command:  
    `cargo build --target thumbv7em-none-eabihf`


- to install target use: `rustup target add thumbv7em-none-eabihf`  
- to list all supported targets use: `rustup target list`  
- some targets for crosscompilation:  
        thumbv6m-none-eabi  
        thumbv7em-none-eabi  
        thumbv7em-none-eabihf  
        thumbv7m-none-eabi  
        thumbv7neon-linux-androideabi  
        thumbv7neon-unknown-linux-gnueabihf  
        thumbv8m.base-none-eabi  
        thumbv8m.main-none-eabi  
        thumbv8m.main-none-eabihf  

    *`thumb` - name of the instructions*  
    *`v6m, v7m/em, v8m` - [arm core architecture](https://en.wikipedia.org/wiki/ARM_architecture_family)*  
    *`eabi` - embedded application binary interface*  
    *`hf` - hard floating (FPU operations will performed (faster) on the hardware level)*  
    *`sf` - soft floating (FPU operations will performed (slower) on the software level)*  