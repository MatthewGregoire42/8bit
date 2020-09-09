## 8-bit computer project

In the summer of 2019, I set out to build an 8-bit CPU on breadboards. This project was enabled by Ben Eater's [series of tutorials](https://eater.net/8bit), which is a phenomenal walkthrough of the entire process. In the process I've learned a lot about how computers work at the lowest level, and have put into practice a lot of what I've learned in computer architecture classes.

![Image of Computer](https://github.com/MatthewGregoire42/8bit/blob/master/20190730_151454.jpg)

## Differences from Ben Eater's design

By and large, my build is the same as the one Ben Eater makes in his tutorial series. However, there are a few differences.

* The location of the bus display is near the lower left-hand corner, as I don't trust my soldering skills enough to precisely solder 8 LEDs and 8 resistors together.

* Each bus line is tied to ground through a 1k resistor, instead of 10k. I found that with 10k resistors there were a lot of floating voltages on the bus, causing unpredictable behavior.

* When I first hooked up most of the components to the bus, the program counter circuit started counting erratically. It turns out that this was due to the RC circuit in the RAM module that detects the rising clock edge. In order to fix this, I buffered the clock signal with two NAND gates (conveniently located nearby), which isolated the RC circuit from the rest of the computer.

* Power distribution and grounding were big issues with a breadboard project of this size. I found that 5 volts on one corner of the computer meant something noticably different than 5 volts in the opposite corner. This issue was mitigated by improving connections between all the power rails on the computer, and by adding small decoupling capacitors on every rail. In the same vein, the contents of the flags register are being buffered through an extra XOR chip (by being XORed with 0) into the control logic, because of this voltage drift.

* I was having issues with values disappearing from memory. I assume this was because a capacitor in an RC edge-detection circuit for the memory's write signal was discharging for a fraction of a second longer than expected, which led to the default value of 0x00 being written to memory. In order to fix this, I reduced the corresponding resister from 1000 ohms to 100 ohms. This way the capacitor discharges 10 times as fast, and I haven't been having problems with this since.

## Ongoing issues

Most programs that I've ran on this computer work great! It can add a couple numbers, count up by a certain increment, conditionally jump, and even compute the Fibonacci sequence. However, when running some programs (such as one to multiply two numbers together), values in memory sometimes get corrupted. My efforts to fix this are ongoing.

## Programs

16 bytes of memory is an austere limitation to work with, but I've managed to run some interesting programs on this computer. To get a feel for the assembly language, here is an example program that adds two values `x` and `y` together.

    0  LDA 14   # Loads the value in memory address 14 into the A register
    1  ADD 15   # Adds the contents of memory address 15 into the A register
    2  OUT      # Outputs the value in the A register
    3  HLT      # Halts the program
    ...
    14 x
    15 y
    
For another example, here is a program Ben Eater walks through in his videos, to count upwards by threes.

    0 LDI 3     # Loads the value 3 into the A register
    1 STA 15    # Stores the value of the A register into address 15
    2 LDI 0     # Resets the A register to 0
    3 ADD 15    # Adds the contents of memory address 15 into the A register
    4 OUT       # Outputs the value in the A register
    5 JMP 3     # Jump back to address 3 to continue executing
    
These programs (and others) are described in detail in the video series. I also recommend [this blog post](https://theshamblog.com/programs-and-more-commands-for-the-ben-eater-8-bit-breadboard-computer/) for more interesting programs to run on this computer.

One that I came up with myself is a program to calculate all of the triangular numbers under 255. Here is my code:

    0	LDA 15
    1	ADD 14
    2	JC  9
    3	OUT
    4	STA 15
    5	LDA 14
    6	ADD 13
    7	STA 14
    8	JMP 0
    9	LDI 0
    10	STA 15
    11	LDA 13
    12	STA 14
    13	1
    14	1
    15	0

As written, this program goes through all triangular numbers under 255, then loops. In order to simply calculate the largest triangular number under 255, simply replace lines 9-14 with one `HLT` command.

## Future work

In the future, if I have the time, here are some improvements I'd like to make:

- Increase the capability of the ALU, to support logical operations and shifts
- Add a second instruction register to allow for multiple-byte instructions
- Increase the amount of memory (16 bytes is incredibly limiting!)
- Add a control signal to reset the instruction step counter, to increase the speed of some operations.

Alternatively, I would love to dive deeper into the function of the ALU, and build a fully functional 4-bit ALU from scratch.

## Breakdown of costs

I've invested quite a bit into this project, both in money and time. Most of the cost came from the initial [kit](https://eater.net/8bit/kits) that I ordered, which had most everything I needed to get started. However, the full breakdown is as follows:

- Initial kit: $282.27
- 74LS86 XORs (x10): $4.99
- 74LS86 XORs (DIP): $4.47
- Wire cutters and pliers: $27.89
- Various wires: $33.97
- Multimeter: $26.99
- Spare resistors: $8.79

Total cost: **$389.37**

I had to buy extra XOR chips because I accidentally shorted one that came with the kit. Then I realized that the new chips I ordered weren't in DIP packages, so I had to order more again.
