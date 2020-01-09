# VerilogSkeleton: IN PROGRESS

Skeleton project for Verilog / IceStorm.

The idea is to make top.v the main module for the .bin file, and top_test.v is the testbench top module. You can have any number of submodules - see Makefile for how to organize this.

Implements a blinky for testing, which you can pull out and replace with your own design.

# Usage

Zeroth, clone this repo or grab it as a zip file.
* zip file might be the better way. **If you clone it, be sure to delete the .git subdirectory or else when you change stuff and go to push it it will want to overwrite this repo!**

First, change the `BOARD` value in Makefile.icestorm to the board you plan to use. Find the line that looks like this:

```
BOARD ?= upduino
```

and replace the right-hand side with:
* `tinyfpga` for tinyfpga-bx
* `icestick` for lattice iceStick
* `tomu` for (I think) Fomu / Tomu-FPGA
* `icebreaker` for Icebreaker-FPGA
* `upduino` for Upduino v2/2.1


**IF YOU ARE USING AN ICESTORM-COMPATIBLE BOARD NOT IN THAT LIST, YOU MAY NEED TO ADD A PIN FILE (PCF) AND ADD ITS CONFIGURATION VALUES FOR DEVICE AND PACKAGE TO Makefile.icestorm.**
* for instance, the upduino entry in Makefile.icestorm looks like this, up at the top:
    ```
    DEVICE-upduino ?= up5k
    FOOTPRINT-upduino ?= sg48
    PIN_SRC-upduino ?= upduino_v2.pcf
    ```
* then some way down is a line that says which board is being targeted:
    ```
    BOARD ?= upduino
    ```
* think of a name for your board. For example, `notexist` that is based on iCE40-LP1K-CM49
    ```
    DEVICE-notexist ?= lp1k
    FOOTPRINT-notexist ?= cm49
    PIN_SRC-notexist ?= notexist.pcf
    [...]
    BOARD ?= notexist
    ```
* create or procure a pcf file for your board and call it notexist.pcf (you can call it what you want, jsut so the file name is there in the PIN_SRC line.)
* if different build tools are needed, you'll need to change Makefile.icestorm (will investigate with e.g. Orange Crab or TinyFPGA EX when they become available.)

Currently supported are:
* `BOARD ?= upduino` upduino v2 (default, tested on [Gnarly Grey Upduino v2](http://www.gnarlygrey.com/) and [tinyVision.ai Upduino v2.1](https://www.tindie.com/products/tinyvision_ai/upduino-v21-low-cost-fpga-board/))
* `BOARD ?= icestick` [Lattice iceStick](http://www.latticesemi.com/icestick) (*not yet tested and needs LED blink code*)
* `BOARD ?= tinyfpga` [tinyFPGA BX](https://www.crowdsupply.com/tinyfpga/tinyfpga-bx) (*not yet tested and needs LED blink code*)
    * did install of apio except for the icestorm step here - https://tinyfpga.com/bx/guide.html bc I don't want to overwrite the icestorm/iverilog I have been using (not sure it WOULD do that, but might. Will try later.)
        ```
        pip install apio==0.4.0b5 tinyprog
        apio install system scons icestorm iverilog <==== I DIDN'T DO THIS STEP (YET)
        apio drivers --serial-enable    
        ```
    * At first, tinyprog didn't work. Kept getting device or resource busy
        * Got tinyprog working by following DaveMcEwan's advice on https://github.com/tinyfpga/TinyFPGA-BX/issues/4# - `sudo apt purge modemmanager` did the trick.
* `BOARD ?= icebreaker` [icebreaker FPGA](https://www.crowdsupply.com/1bitsquared/icebreaker-fpga) (doesn't have pcf file, I don't own one to test, needs LED blink code)
* `BOARD ?= tomu` [tomu FPGA - I think this is the right link](https://www.crowdsupply.com/sutajio-kosagi/fomu) (doesn't have pcf file, I don't own one to test, needs LED blink code)

Next, copy the appropriate "top" source file to `top.v`. For instance, if you're building for icestick, do

`cp top-icestick.v top.v`

OR you can change the list of required files in Makefile for the alldeps target from

`alldeps = top.v $(submodules)` to

`alldeps = top-icestick.v $(submodules)`

**(VERIFY THAT THIS WORKS!)**

# modifying to suit

1. Rename files as you like, reflecting changes in Makefile.
1. when you add new module .v files, be sure to add them to alldeps in Makefile.
1. change top.v and top-test.v as needed; I recommend keeping them as much the same as possible - I think "minimal top" is a nice design idea but I'm a n00b and may be totally wrong
    1. However, I recommend putting hardware-specific stuff in top.v 

# building

`make all` is intended to build the .bin output file to send to the target hardware. 
* `iceprog top.bin` works to send it to icestick and upduino v2(.1)
* `tinyprog -p top.bin` for tinyfpga-bx.

toolchain is yosys / nextpnr / icepack

`make test` is intended to build a simulation

`top-test.v` as written will work with all platforms; it doesn't use any of the hardware-specific settings. Try to keep it that way.

toolchain is iverilog / vvp / vcd2fst, yielding a .fst file that can be viewed in gtkwave

`make clean` does the usual cleanup of all the non-source files.

stdout and stderr are redirected during the compile, to build_top_out.txt and build_top_err.txt for the "all" target, to sim_top_tb_out.txt and sim_top_tb_err.txt in the "test" target.

* need to test `make flash` and `make gui` and `make pll`*

Makefile, Makefile.icestorm, and upduino_v2.pcf are copied and modified from osresearch's code at https://github.com/osresearch/up5k licensed under GPL3

tinyfpga-bx.pcf is copied and modified from Luke Valenty's `pins.pcf` code at https://github.com/tinyfpga/TinyFPGA-BX/tree/master/apio_template, licensed under CERN Open Hardware Licence v1.2. 

icestick.pcf is copied and modified from Juan Gonzalez's code at https://github.com/FPGAwars/apio-examples


# test to see if markdown on github can syntax highlight verilog

Oh wow it looks like the Atom previewer, anyway

And indeed github does too!

```verilog
//Based on Dan Gisselquist's blinky at https://zipcpu.com/blog/2017/05/19/blinky.html
`default_nettype none

module blinky(
    input wire i_clk,
    output wire o_led
);

    parameter CBITS = 26;

    reg	[CBITS-1:0]	counter = 0;
    always @(posedge i_clk)
        counter <= counter + 1'b1;
    assign o_led = counter[CBITS-1];
endmodule
```
