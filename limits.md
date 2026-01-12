# Known Limitations

## Hardware

#### Program and data memory sizes

Memory has a size of 4KB. Accessing higher adresses will either cause memory error (if within address space of memory module) or will always return 0 on read.

#### Program space is not mapped onto system bus

There is no way for the CPU to read the program memory other than to fetch the instruction. Also note that program memory is not writable by CPU altogether.

## C compatibility

#### No support global variables

The global variables reside on `.data` (or `.bss` if uninitialized) segment, which is neither uploaded to the device, nor the CPU has any way of extracting the data from program file. The only way to initialize a global variable right now is by writing to it inside code called from main(). In this case, compiler uses ADDI/LUI instructions to create necessary value in register and then writes it to the memory.

The same goes for global constants. To make the code compatible replace this:
```c
static const int magic = 0x42;
```
with macro definitions:
```c
#define MAGIC 0x42
```

Static structs require more elaborate changes, however it can still be done rather easily most of the time.

#### No support for literals

Same as above - literals may reside in .data section. Integer/single char literals almost exclusively cause the compiler to create a value in register, but string literals will not work.

#### No support for multiplication or division

GCC might attempt to generate `mul*` or `div*` instructions when using standard C `*` and `/` operators. Use bit-shifts instead.

#### No floating point numbers

There is no circutry to handle floating point arithemtic - use fixed point decimals, some basic functionality is provided in `fxp32.h` and `fastfxp32.s`.

#### No standard library

The standard library has not been reimplemented for this system, since it is too large to fit into the program memory. There are a variety of similar, simpler functions for printing on terminal (no file support) and other I/O. 

Peripherals definitions are provided in `sysdev.h`. It also includes `stdint.h` for explicit integers typedefs. 
