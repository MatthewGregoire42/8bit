## 8-bit computer project

In the summer of 2019, I set out to build an 8-bit CPU on breadboards. This project was enabled by Ben Eater's [series of tutorials](https://eater.net/8bit), which is a phenomenal walkthrough of the entire process. In the process I've learned a lot about how computers work at the lowest level, and have put into practice a lot of what I've learned in computer architecture classes.

## Differences from Ben Eater's design

By and large, my build is the same as the one Ben Eater makes in his tutorial series. However, there are a few differences.

The location of the bus display is near the lower left-hand corner, as I don't trust my soldering skills enough to precisely solder 8 LEDs and 8 resistors together.

Each bus line is tied to ground through a 1k resistor, instead of 10k. I found that with 10k resistors there were a lot of floating voltages on the bus, causing unpredictable behavior.

When I first hooked up most of the components to the bus, the program counter circuit started counting erratically. It turns out that this was due to the RC circuit in the RAM module that detects the rising clock edge. In order to fix this, I buffered the clock signal with two NAND gates (conveniently located nearby), which isolated the RC circuit from the rest of the computer.

Power distribution and grounding were big issues with a breadboard project of this size. I found that 5 volts on one corner of the computer meant something noticably different than 5 volts in the opposite corner. This issue was mitigated by improving connections between all the power rails on the computer, and by adding small decoupling capacitors on every rail. In the same vein, the contents of the flags register are being buffered through an extra XOR chip (by being XORed with 0) into the control logic, because of this voltage drift.

## Ongoing issues

Most programs that I've ran on this computer work great! It can add a couple numbers, count up by a certain increment, conditionally jump, and even compute the Fibonacci sequence. However, when running some programs (such as one to multiply two numbers together), some values disappear from memory. This may be related to the issue with voltages.


## Future work

Next summer, if I have the time, here are some improvements I'd like to make:

- Increase the capability of the ALU, to support logical operations and shifts
- Add a second instruction register to allow for multiple-byte instructions
- Increase the amount of memory (16 bytes is incredibly limiting!)
- Add a control signal to reset the instruction step counter, to increase the speed of some operations.

## Breakdown of costs

I've invested quite a bit into this project, both in money and time. Most of the cost came from the [kit](https://eater.net/8bit/kits) that I ordered, which had most everything I needed to get started. However, the full breakdown is as follows:

- Initial kit: $282.27
- 74LS86 XORs (x10): $4.99
- 74LS86 XORs (DIP): $4.47
- Wire cutters and pliers: $27.89
- Various wires: $33.97
- Multimeter: $26.99
- Spare resistors: $8.79

Total cost: **$389.37**

I had to buy extra XOR chips because I accidentally shorted one that came with the kit. Then I realized that the new chips I ordered weren't made to fit on breadboards, so I had to order more again.
