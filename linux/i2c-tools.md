# Linux I2C Utilites

`sudo apt install i2c-tools`

## avalible commands

- i2cdetect,  

listing all the I2C buses of the system:  
`i2cdetect -l`

output:

    ```bash
    [root]$ i2cdetect -l
    i2c-1   i2c       0b234500.i2c-bus            I2C adapter
    i2c-2   i2c       0b234580.i2c-bus            I2C adapter
    ```

take i2c bus addres from the output above (i2c-**1**) - address 1 for exanple
and list all devices on this bus address:  
`i2cget -y 0`

output:

    ```
    [root]$ i2cdetect -y 0
        0  1  2  3  4  5  6  7  8  9  a  b  c  d  e  f
    00:          -- -- -- -- -- -- -- -- -- -- -- -- --
    10: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    20: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    30: 30 -- -- -- -- -- 36 -- -- -- -- -- -- -- -- --
    40: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    50: 50 -- 52 -- -- -- -- -- -- -- -- -- -- -- -- --
    60: -- -- -- -- -- -- -- -- -- -- -- -- -- -- -- --
    70: -- -- -- -- -- -- -- --
    ```

- i2cdump,
- i2cget,

i2cget can be used to read the I2C slave device, for example from slave address(0x50) on bus 0

    ```
    [root]$ i2cget -y 0 0x50 0
    0x23
    ```

- i2cset,

Read the device at offset 0x2
Write the 0x12 at offset 0x2 of slave device 0x68
Read back the device at offset 0x2 and verify the data should be 0x12.

    ```
    1.Read the device at offset 0x2.
    [root]$ i2cget -y 1 0x68 0x2
    0x14
    [root]$

    2.Write the 0x12 at offset 0x2 of slave device 0x68
    [root]$ i2cset -y 1 0x68 0x2 0x12
    [root]$

    3.Read back the device at offset 0x2 and verify the data should be 0x12.
    [root]$ i2cget -y 1 0x68 0x2
    0x12
    [root]$
    ```

- i2ctransfer

## references

- [I2C Utilities in Linux on linuxhint.com](https://linuxhint.com/i2c-linux-utilities/)