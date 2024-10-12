# Intel 8051 Assembler Directory

In this directory I keep the basic tools for development in 8085 on a Windows 64 environement. I also keep a program I devised 40 years ago but that I never had an opportunity to develop. Finally, now I was able to implement it using the tools shown here. This program is given as an example how to use the tools.

## Development Tools

1. **asm51.exe**: DOS 8051 Assembler. Converts 8051 Assembler instructions into .obj file
2. **oh.exe**: DOS program to convert an 8051 .obj file into an Intel .hex file. The converted files are used in simulators and emulators.
3. **EdSim51DI**: a Java 8051 simulator that allows loading Intel .hex files to be able to debug or execute 8051 programs.

## Suport tools

Unfortunately, **asm51.exe** and **oh.exe** don't work on Windows 64. To run these programs one must use a DOS emulator, such as 

1. **dosbox.exe**: DOS emulator to allow using **asm51.exe** and **oh.exe** on Windows 64 machines. The installation is easy. Just copy the contents of the zip file suplied in this repository to a directory DOSBOX (for example) in Documents for better protect your programs. Run **dosbox.exe**. It will stop saying that it was not configured. Just click to configure, and press return to accept all configurations by default it suggests. It then opens a DOS window in the C: drive. The C: drive is actually the diretctory C inside DOSBOX. Copy **asm51.exe** and **oh.exe** to DOSBOX/C.

## Running asm51.exe and oh.exe

Once the DOS emulator is installed in directory DOSBOX, (as suggested above), and **asm51.exe** as well as **oh.exe** are copied to DOSBOX/C, to assemble a program called `your_program.asm` (where it was previously typed your 8051 Assembly code) just type:

```
asm51 your_program.asm
```
After that, if your program is without error and the .obj is generated, just run **oh.exe** (to generate the .hex file) by typing:

```
oh your_program.obj
```

## Installing and running EdSim51DI

EdSim51DI emulator is given in a zip file in this directory. The zip file contains a directory called **edsim51di** containing the file `edsim51di.jar` and a `lib` directory. Copy both together in some location you want to run your 8051 program.

To run `edsim51di.jar` you need Java JVM installed in your machine. To run it just double click `edsim51di.jar`. For the sake of simplicity, though, one could also run the simulator by typing in a command tool opened in the directory where `edsim51di.jar` is located:

```
java -jar edsim51di.jar
```

# Example hexasc.asm

This example illustrates how to convert a byte in hexadecimal contained in the registers (or memory) to two bytes ASCII characters representing this byte. This code has been designed to be used in the industry to display the hexadecimal contents in a 8051 microcontroller in some device such as a console. Since raw binary values are not printable, one has to convert them into ASCII characters. I've seen many different convertion procedures to do that, included one that was suggested in the 8051 Databook itself. The solution I designed here, though, is branchless, as well as far more compact and faster. The logic is quite intrincate, though, and that's why it is a good example to use the simulator suggested above.

First of all, one needs to assemble the program running the 8051 Assembler in the way shown below. On Win64, one need to type this into the **dosbox** console: 

```
asm51 hexasc.asm
```
After the .obj is generated, just run **oh.exe** (to generate the .hex file) by typing:

```
oh hexasc.obj
```

Copy hexasc.hex to the same directory where **edsim51di** is stored.

Once **edsim51di** is running, one loads the program in this way:






