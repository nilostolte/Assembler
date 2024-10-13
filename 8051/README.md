# Intel 8051 Assembler Directory

In this directory I keep the basic tools for development in 8051 on a Windows 64 environment. I also keep a program I devised in 1985/86 but that I never had an opportunity to implement. Finally, now I was able to run it using the tools shown here. This program is given as an example how to use the tools.

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

This example illustrates how to convert a byte in hexadecimal contained in the registers (or memory) to two bytes ASCII characters representing the hexadecimal value. This code has been designed to be used in the industry to display the hexadecimal contents in a 8051 microcontroller in some device such as a console. Since raw binary values are not printable, one has to convert them into ASCII characters. I've seen many different convertion procedures to do that, included one that was suggested in the 8051 Databook itself. The solution I designed and that's shown here, though, is branchless, as well as far more compact and faster. The logic is quite intrincate, though, and that's why it is a good example to use the simulator suggested above. I've shown and explained this code in 1985/86 to a company of my knowledge. The company changed all their hexadecimal to ASCII conversion code by this implementation. It's fun to see that 8051 is still used nowadays. This was another reason to propose this repository and this example as a small case study.

First of all, one needs to assemble the program running the 8051 Assembler in the way shown below. On Win64, one needs to type this into the **dosbox** console: 

```
asm51 hexasc.asm
```
After the `hexasc.obj` is generated, just run **oh.exe** (to generate the `hexasc.hex` file) by typing:

```
oh hexasc.obj
```

One could have bypassed this process by loading `hexasc.asm` file directly into the simulator (since it also has an 8051 Assembler embedded). However, as far the traditional way of proceding is concerned, it is better to use the original Intel development tools, since the object code generated is absolutely the same as one would execute in a real microcontroller. This means that these tools can also be used professionally. They aren't only for educational purposes.

Since `hexasc.hex` has been created for this purpose, all one needs to do is to copy this file to the same directory where **edsim51di** is stored.

Once **edsim51di** is running, one loads the program by clicking in 1, 2, and 3:

<img src="https://github.com/user-attachments/assets/d896c291-3b0c-4cba-83fc-c24a7ade018f" width=512><br>


Once loaded, one clicks on `Assm` to write the machine code into memory:
 
<img src="https://github.com/user-attachments/assets/47cee166-5e39-4d03-9686-6edbf1c1ce09" width=512><br>

Now the button `Step` shows up. The machine code also appears in `Code Memory` (if `Code Memory` doesn't appear, click on `Data Memory` to toggle between both). Notice also the editable fields `addr` and `value` in `Modify Code` (it's `Modify RAM`in `Data Memory`). The field `addr` in the image was already modified to address `0x0001` (for that, type `0x0001` instead of `0x0000` in the field `addr` and press `Enter`). `Modify Code` shows the value `0x4A`, which is the initial immediate byte to be transformed in ASCII, initially moved to the register `R1` in the first instruction of the program:

```
MOV R1,#4AH
```

By modifying `value` field directly in `Code Memory`, by typing the new value and pressing `Enter`, the program once executed will convert the new value to the corresponding ASCII codes. For example, `0x4A` will be converted to the two ASCII bytes: `0x3441`, which is the number 4 followed by the letter A. Once the program is executed, register `R1` will contain the input, `R0` will contain the first ASCII byte `0x34`, and the accumulator (`ACC`) will contain the second ASCII byte `0x41`. One can modify the value to `0x4B`, `0x4C`, `0x4D`, `0x4E`, `0x4F`, or any other value to test the program.

<img src="https://github.com/user-attachments/assets/f076d3fb-a5b1-4d50-be1d-0029adc35f1a" width=512><br>

The instructions following the first line of code, just copies the input byte into the accumulator, stores the higher hexadecimal nibble into the lower hexadecimal nibble, and resets the higher nibble:

```
	MOV  A,R1
	ANL  A,#0F0H
	SWAP A
```

What follows is the conversion from the hexadecimal value in the lower nibble to a full byte ASCII character:

```
 	DA   A
 	MOV  C,ACC.4
 	ADDC A,#30H
```
The whole idea here is to use `DA A`, which corresponds to the Decimal-adjust Accumulator mnemonic, for a different purpose than BCD arithmetic. All this instruction does is to add 6 to the the accumulator if the lower nibble is greater than 9. 

I use this here because hexadecimal digits A to F do not sequentially follow characters 1 to 9 in ASCII, but are separated by 7 characters in between both (characters ':', ';', '<', '=', '>', '?', and '@'). But as one can see, adding only 6 is not enough. For a correct result for hexadecimal digits greater than 9, an extra 1 has to be added in these cases. 

We notice that, in all cases of values greater than 9, we will obtain a 1 in bit 4 of the accumulator, because adding 6 to any value greater than 9 will always be 16 plus the value of all remaining bits in the lower nibble. Therefore, in these cases, the higher nible will always have 1 as its value, and only bit 4 will be set. In the cases of values less or equal to 9, the higher nibble will be zero and the bit 4 will then be also zero.

Because of this fact, and since the 8051 allows copying any bit in the registers to the flags, all is needed is to copy bit 4 of the accumulator to the carry flag, and then adding `0x30` with the carry to the accumulator. In this way when the hexadecimal value to be converted is lower or equal than 9, the ASCII character will be between `0x30` and `0x39`, that is, between '0' and '9', since one adds `0x30` to values between 0 and 9. If the hexadecimal value to be converted is greater than 9, the ASCII characters will be between `0x41` and `0x46`, that is, between 'A' and 'F', since we are adding 6, the carry (in this case always 1) and `0x30`(in other words we are simply adding `0x37`) to values between A and F.

One can see that the conversion part is repeated for the second ASCII character, since the algorithm is essentially the same. Notwithstanding, the code is so short that one can simply repeat it without any penalty in size, much less in execution time. Also of note, the code to move the proper nibbles to their designated places are as long as the conversion parts, which seems quite unexpected.

The code in between both conversions, merely saves the previous answer to `R0` and copies the original input to the accumulator, filtering the higher nibble out, as can be seen here:

```
	MOV  R0,A
	MOV  A,R1
	ANL  A,#0FH
```

To check how all this works fine, one can check any desired values besides `0x4A` as explained above and execute the program step by step clicking on the buttom step. To restart from the first instruction with new values, just press RST buttom, change the value as instructed above, and start pushing Step buttom to execute step by step.
