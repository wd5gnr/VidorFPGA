# VidorFPGA

Read more about this fork: https://hackaday.com/2018/11/01/hands-on-with-the-arduino-fpga/

This repository includes FPGA IP Blocks compatible with the Arduino Vidor family of products and is aimed to users already familiar with FPGA development process.
FPGA development using native tools, although encouraged, is not supported by Arduino as it is quite complex difficult to support. If you feel this challenge is for you please know that we can only provide very limited support as our main efforts will be targeted at providing a smooth experience within Arduino IDE and Arduino Create through precompiled libraries and with the web tool that will provde an easy way to assemble IP blocks.

## Directory structure
directory structure is summarized in the following table:

Directory  | Contents
---------- | --------
ip | source code for IP blocks
projects | sample project files for the various boards
constraints | constraint files for the various boards. includes pinout and timings

Note: After the Al Williams Fork this is all under vidordemo. The other directories are:
* C - The C program for creating app.h
* Shell - Awk program if you don't like C
* EmptySketch - A companion sketch for the demo

Read more about this fork below.

## Things to know before getting started
Once again this repository is intended only for people already familiar with FPGA programming. At the moment the primary intent is to disclose IP block functionality and present the infrastructure we created so that potantial contributors can start to evaluate it. As of today this repository does not contain full source code required to compile the released libraries as parts of it requires some more polishing both in terms of code and in terms of licensing (in some cases from third parties). 
Full examples of working FPGAs, along with instructions to create a library and access the FPGA, will be posted here but will not necessarily reflect the official images we are publishing.

## Getting started
The prerequisite to compile MKRVIDOR4000 board FPGA images is Quartus II 18.0 Lite or Standard which can be downloaded from Altera/Intel web site.
Once Quartus is installed you can open a project under the projects directory and compile it with Quartus. 

Quartus will produce a set of files under the output_files directory in the project folder. In order to incorporate the FPGA in the Arduino code you need to create a library and preprocess the ttf file generated by Quartus so that it contains the appropriate headers required by the software infrastructure. Details of this process will be disclosed as soon as the flow is stable.

Programming the FPGA is possible in various ways:
1. flashing the image along with Arduino code creating a library which incorporates the ttf file
1. programming the image in RAM through USB Blaster (this requires mounting the FPGA JTAG header). this can be done safely only when SAM D21 is in bootloader mode as in other conditions it may access JTAG and cause a contention
1. programming the image in RAM through the emulated USB Blaster via SAM D21 (this component is pending release)

## Fork by Al Williams (Hackaday)
I forked this to create a slightly different way of doing things. Here's the changes:
1. Changed the top .v file to include user.v so you get access to the top-level I/O without having to edit the boilerplate
2. Turned off Smart Compile so Quartus won't skip rebuilding your code when only the include file changes
3. Created new vidorcvt C program to do the translation of the binary output
4. Created awk script to do the same operation if you don't like the C program
4. Simplified example plus "blank template" for both FPGA and sketch

## About Vidorcvt
The Java program provided by Arduino was compiled so it only works with Java 11 and it is very simple anyway. It just
flips bit order. So I rewrote it very quickly in C. You should be able to compile it with:
    gcc -o vidorcvt vidorcvt.c

Note that if you look at the code you could probably convert it to any language you like -- it is very trivial. There are no arguments
so you'll need to redirect as in:
    vidorcvt <binaryfile.ttf >app.h
	
If you prefer to use awk, you can find the awk equivalent in the shell subdirectory.	
	
## About the Example Project/Sketch
The vidordemo directory has a Quartus project that blinks an LED on D6 at two rates, depending on the state of D5.

The blink-sketch is a simple sketch that lets you control the blink rate by treating D5 as an output. It also sets D6 
as an input and monitors the LED blinking status. This shows two-way communication with the FPGA.
Note: If you stop the sketch from driving D5 you could use a wire or switch to control the LED blink rate. However,
if the sketch drives the pin like it does now, do not try to manually force D5 high or low.

## On Your Own
You should be able to change user.v and the provided Sketch to handle your own designs. It would be simple to use, for example, SPI to communicate between the FPGA and the CPU. I left the IP directory in from Arduino if you need any of those components.
