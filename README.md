# VerilogSkeleton

Skeleton project for Verilog / IceStorm.

# Usage

`make all` is intended to build the .bin output file to send to the target hardware

toolchain is yosys / nextpnr / icepack

`make test` is intended to build a simulation 

toolchain is iverilog / vvp / vcd2fst, yielding a .fst file that can be viewed in gtkwave

*need to test `make flash` and `make gui` and `make pll`*

Makefile, Makefile.icestorm, and upduino_v2.pcf are copied and modified from osresearch's code at https://github.com/osresearch/up5k licensed under GPL3
