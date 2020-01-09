# VerilogSkeleton

Skeleton project for Verilog / IceStorm.

The idea is to make top.v the main module for the .bin file, and top_test.v is the testbench top module. You can have any number of submodules - see Makefile for how to organize this.

# Usage

First, change the `BOARD` value in Makefile.icestorm to the board you plan to use.

**YOU MAY NEED TO ADD ITS CONFIGURATION VALUES FOR DEVICE AND PACKAGE TO Makefile.icestorm.**

Currently supported are:
* `BOARD ?= upduino` upduino v2 (default, tested on [Gnarly Grey Upduino v2](http://www.gnarlygrey.com/) and [tinyVision.ai Upduino v2.1](https://www.tindie.com/products/tinyvision_ai/upduino-v21-low-cost-fpga-board/))
* `BOARD ?= icestick` iceStick (*not yet tested and needs LED blink code*)
* `BOARD ?= tinyfpga` tinyFPGA BX (*not yet tested and needs LED blink code*)
* `BOARD ?= icebreaker` icebreaker (doesn't have pcf file, I don't own one to test, needs LED blink code)
* `BOARD ?= tomu` tomu (doesn't have pcf file, I don't own one to test, needs LED blink code)

Next, copy the appropriate "top" source file to `top.v`. For instance, if you're building for icestick, do

`cp top-icestick.v top.v`

OR you can change the list of required files in Makefile for the alldeps target from

`alldeps = top.v $(submodules)` to

`alldeps = top-icestick.v $(submodules)`

**(VERIFY THAT THIS WORKS!)**

`make all` is intended to build the .bin output file to send to the target hardware

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
