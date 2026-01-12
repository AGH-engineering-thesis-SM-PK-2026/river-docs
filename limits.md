# Known Limitations

## Hardware

#### Program and data memory sizes

CPU cannot access more than 1024 words (4096 bytes) of both program and data memory. Overflows will not saturate and will wrap (eg. address 4096 becomes 0).

#### Non-word memory access

Currently the memory modules does not permit non-word aligned reads/writes. This means these instructions: `sb`, `sh`, `lb`, `lh` will cause hardware exceptions, halting the program, or lead to unexpected program behaviour.

This problem might be fixed in the future.

Never-the-less other peripherals might support reading or writing bytes or half-words to designated bus device location. The peripherals are more free to do anything with the address or data and can ignore parts/one of them/both of them.

#### Program space is not mapped onto system bus

There is no way for the CPU to read the program memory other than to fetch the instruction. Also note that program memory is not writable by CPU altogether.

## C compatibility

#### No support global variables

The global variables reside on `.data` (or `.bss` if uninitialized) segment, which is neither uploaded to the device, nor the CPU has any way of extracting the data from program file.

To make the code compatible replace this:
```c
static const int magic = 0x42;
```
with macro definitions:
```c
#define MAGIC 0x42
```

Static structs require more elaborate changes, however it can still be done rather easily most of the time.

#### Risky `char` and `short`

There is no guarantee, even with `register` that the variables of types `char` and `short` will not be placed on stack - therefore requiring non-word memory access, and possibly causing either crashes or memory corruption.

#### No support for multiplication or division

GCC might attempt to generate `mul*` or `div*` instructions when using standard C `*` and `/` operators. Use bit-shifts instead.

#### No floating point numbers

There is no circutry to handle floating point arithemtic - use fixed point decimals, some basic functionality is provided in `fxp32.h` and `fastfxp32.s`.

#### No standard library

The standard library has not been reimplemented for this system, since it is too large to fit into the program memory. There are a variety of similar, simpler functions for printing on terminal (no file support) and other I/O. 

Peripherals definitions are provided in `sysdev.h`. It also includes `stdint.h` for explicit integers typedefs. 
