# Guide


## Setup

#### Note for Windows users

To compile assembly and C programs, the *riscv-none-elf* toolchain needs to be installed on your system. Unfortunately Windows binaries are not available and so the recommended approach is to use WSL and install the toolchain there.

### Get the source

Clone the repo:

```sh
git clone https://github.com/
```

Alternatively go to Go to [RIVER repo](https://github.com/) and clone via webpage. This is the recommended way, since all the tools, hardware and userland source code are ensured to be compatible with each other.

### Install the *Vivado* suite

Go to AMD's [*Vivado* download page](https://www.xilinx.com/support/download.html) and get the latest release.

### Install the *rv* build script

> For WSL users: Alpine Linux is recommended and the `rv.sh` is proven to work on this distro. Use this [tutorial](https://averylarsen.com/posts/install-alpine-wsl/) to get obtain it.

Install the following packages:

| Distro / OS     | Command line                                              |
| --------------- | --------------------------------------------------------- |
| Alpine Linux    | `apk add gcc-riscv-none-elf `                             |
| Fedora Linux    | `dnf install binutils-riscv64-linux-gnu` (?)              |
| Arch Linux      | `pacman -S riscv32-elf-binutils`                          |
| Void Linux      | not supported                                             |
| OpenBSD         | `pkg_add riscv-elf-gcc` (?)                               |
| FreeBSD         | `pkg install riscv32-unknown-elf-gcc` (?)                 |

> Unfortunately the packages differs slightly between the distros and OSes, necessitating aliases to `gcc`, `as` and `objdump` binaries on some platforms. As an example on Linux Arch, the user would need to add this to their `~/.profile`:
>
>```sh
>alias riscv-none-elf-gcc=risv32-elf-gcc
>alias riscv-none-elf-as=risv32-elf-as
>alias riscv-none-elf-objdump=risv32-elf-objdump
>```
>
> There are ongoing efforts for universal build tool that is able to workaround these inconsistencies.

Next make sure you're in your home directory and download and configure the `rv.sh`:

```sh
cp rv.sh $HOME/rv.sh
chmod +x $HOME/rv.sh
echo "alias rv=$HOME/rv.sh" >> ~/.profile
```

Test the installation:

```sh
rv -h
```

### Install the *rivctl* core manager

The *rivctl* core manager can be used from both Windows and Linux directly. You can run it with:

```sh
# on Windows:
python rivctl/rivctl_vXYZ_en.pyz COM5
# on Linux:
python rivctl/rivctl_vXYZ_en.pyz /dev/ttyACM0
```

Use *'Shift-H'* to view built-in help dialog.

You might need to install USB-to-UART converter drivers. Refer to the manufacturer's manual.

## Run


Open `riscv-core` in *Vivado* suite. Click *Generate bitstream* in the leftmost pane. Then navigate to *Hardware Manager* also in leftmost pane and connect to device automatically (`Open target > Auto Connect`).

Connect both the *ZYBO* board with USB cable and the USB-to-UART converter, according to the following schematic:

```
      JE                   |=[]===[==]=|
[ ][ ][ ][ ][ ]            |]  ZYBO   [|
[ ][G][R][T][ ]            |],,,, ....[|
                           |[]_[]_[]_[]|
G - Ground                  ^^  
T - TX   R - RX             JE 
```

Turn the *ZYBO* board on and program the device with the bitstream. *LD0*, *LD1*, *LD2* should light up, while the *LD3* should remain extinguished. The core is now running.

Open *rivctl* and hit *'Shift-U'*. A file selector will show up. Navigate to parent directory (select `..` at the top and hit *'Enter'*) and to the `rv-usr/out` directory. This directory contains prebuilt *.riv* files, that can be uploaded directly to the MCU via *rivctl*. Select one of these files and upload it to the device.

With the upload finished, you can then run the program by hitting the *'z'* button, or single step via the *'a'* button.
