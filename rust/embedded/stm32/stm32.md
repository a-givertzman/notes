# stm32

* ## Development documentation  

  * [STM32 Nucleo-144 boards (MB1137) User manual](docs/um1974-stm32-nucleo144-boards-mb1137-stmicroelectronics.pdf)
  * [Getting started with STM32 Nucleo board software](docs/um1727-getting-started-with-stm32-nucleo-board-software-development-tools-stmicroelectronics.pdf)
  * [Reference manual](docs/rm0410-stm32f76xxx-and-stm32f77xxx-advanced-armbased-32bit-mcus-stmicroelectronics.pdf)
  * [Programming manual](docs/pm0253-stm32f7-series-and-stm32h7-series-cortexm7-processor-programming-manual-stmicroelectronics.pdf)
  * [on Arm Developer](https://developer.arm.com/documentation/#&cf[navigationhierarchiesproducts]=%20IP%20Products,Processors,Cortex-M)

  * [ARM Cortex-M]
  * [ARM Cortex-M Instruction sets]

* ## Build (cross compilation)

    [chech github for detales](https://github.com/rust-embedded/cortex-m-quickstart)  

    [.cargo/config:](https://github.com/rust-embedded/cortex-m-quickstart#:~:text=Set%20a%20default%20compilation%20target.%20There%20are%20four%20options%20as%20mentioned%20at%20the%20bottom%20of%20.cargo/config.%20For%20the%20STM32F303VCT6%2C%20which%20has%20a%20Cortex%2DM4F%20core%2C%20we%27ll%20pick%20the%20thumbv7em%2Dnone%2Deabihf%20target.)

    ```toml
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

  * or use build command:  
        `cargo build --target thumbv7em-none-eabihf`

  * to install target use: `rustup target add thumbv7em-none-eabihf`  
  * to list all supported targets use: `rustup target list`  
  * some targets for crosscompilation:  

    ```bash
    thumbv6m-none-eabi  
    thumbv7em-none-eabi  
    thumbv7em-none-eabihf  
    thumbv7m-none-eabi  
    thumbv7neon-linux-androideabi  
    thumbv7neon-unknown-linux-gnueabihf  
    thumbv8m.base-none-eabi  
    thumbv8m.main-none-eabi  
    thumbv8m.main-none-eabihf  
    ```

    *`thumb` - name of the instructions*  
    *`v6m, v7m/em, v8m` - [arm core architecture](https://en.wikipedia.org/wiki/ARM_architecture_family)*  
    *`eabi` - embedded application binary interface*  
    *`hf` - hard floating (FPU operations will performed (faster) on the hardware level)*  
    *`sf` - soft floating (FPU operations will performed (slower) on the software level)*  

  * ELF files and bin files?  
    bin file - is how the memory looks before CPU starts executing  
    ELF file - is a cut-up/compressed version of that, wich can't be run by CPU/MCU directly  
    <https://doc.rust-lang.org/cargo/commands/cargo-build.html>
    <https://en.wikipedia.org/wiki/Executable_and_Linkable_Format>
    <https://stackoverflow.com/questions/2427011/what-is-the-difference-between-elf-files-and-bin-files>  

      to read ELF file use:  
      `readelf -h ./target/thumbv7m-none-eabi/release/stm32_hello`  
      to convert ELF into a bin file use:  
      `arm-none-eabi-objcopy -O binary  src-elf-file  target-bin-file.bin`

* ## Debugging  

    for debian / ubuntu install this tools:  
    `sudo apt install gdb-multiarch openocd`  
    if using virtual controller: `qemu-system-arm`

    run command:  
    `openocd -f interface/stlink.cfg -f target/stm32f3x.cfg`  

* ## Run in QEMU

  ```bash
  qemu-system-arm \
    -cpu cortex-m3 \
    -machine lm3s6965evb \
    -nographic \
    -semihosting-config enable=on,target=native \
    -kernel target/thumbv7m-none-eabi/debug/examples/hello
  ```

* ## I2C

  * References:  
  
    * [Rust Discovery Book - Chapter 14 - I2C](https://docs.rust-embedded.org/discov...)
    * [Understanding the I2C bus by Texas Instruments](https://www.ti.com/lit/an/slva704/slv...)
    * [LSM303AGR](https://www.st.com/resource/en/datash...)
    * [LSM303DLHC](https://www.st.com/resource/en/datash...)
    * [F3Discovery electrical schematics](https://docs.rs-online.com/5192/09007...)

    * [about I2C on docs.rust-embedded.org](https://docs.rust-embedded.org/discovery/f3discovery/14-i2c/index.html)  

    * [example on youtube](https://www.youtube.com/watch?v=j3wlSGP5Sw8&list=PLL2SCPK5xSRWBPj-nKOVYIhxRw7C4kYeI&index=6)  

    * Master -> Slave  
      If the master wants to send data to the slave:

      ![I2C Master send to Slave diagram](https://upload.wikimedia.org/wikipedia/commons/3/3e/I2C.svg)  
        `SDA - data line`  
        `SCL - clock line`  

        1. Master: Broadcast START
        2. M: Broadcast slave address (7 bits) + the R/W (8th) bit set to WRITE
        3. Slave: Responds ACK (ACKnowledgement)
        4. M: Send one byte
        5. S: Responds ACK
        6. Repeat steps 4 and 5 zero or more times
        7. M: Broadcast STOP OR (broadcast RESTART and go back to (2))

    * Master <- Slave  
      If the master wants to read data from the slave:  
      ![I2C Master read from Slave diagram](https://upload.wikimedia.org/wikipedia/commons/3/3e/I2C.svg)  
        1. M: Broadcast START
        2. M: Broadcast slave address (7 bits) + the R/W (8th) bit set to READ
        3. S: Responds with ACK
        4. S: Send byte
        5. M: Responds with ACK
        6. Repeat steps 4 and 5 zero or more times
        7. M: Broadcast STOP OR (broadcast RESTART and go back to (2))

* ## USB OTG

  * [usb-otg-workspace github](https://github.com/Disasm/usb-otg-workspace)
  * [example](https://dev.to/minkovsky/universally-stupid-battling-usb-on-a-microcontroller-with-rust-2leg)

* ## ADC

  * [STM32 Timer + ADC + DMA: Part 1](https://www.skybluetrades.net/blog/2020/11/2020-11-24-stm32-timer-adc-dma-1/)
  * [STM32 Timer + ADC + DMA: Part 2](https://www.skybluetrades.net/blog/2020/11/2020-11-25-stm32-timer-adc-dma-2/)
  * [STM32 Timer + ADC + DMA: Part 3](https://www.skybluetrades.net/blog/2020/11/2020-11-26-stm32-timer-adc-dma-3/)

  * [STM32 I2S ADC DMA & Double Buffering - Digital Audio Processing with STM32 #4 - Phil's Lab #55](https://www.youtube.com/watch?v=zlGSxZGwj-E&t=440s)

[ARM Cortex-M]: https://en.wikipedia.org/wiki/ARM_Cortex-M
[ARM Cortex-M Instruction sets]: https://en.wikipedia.org/wiki/ARM_Cortex-M#Instruction_sets
