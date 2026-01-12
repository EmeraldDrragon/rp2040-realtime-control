# RP2040 Bare-Metal GPIO Driver

GPIO driver for the RP2040, developed by interacting directly
with the hardware registers. The driver is implemented without
relying on Pico-SDK hardware functions. Provides control over
pin function, direction, electrical properties, and logic levels.

## Hardware Architecture and Memory Map

RP2040 maps its peripherals into the global address space.
The driver interfaces with three hardware banks on the RP2040:
 * **IO_BANK0** mapped at 0x40014000 responsible for multiplexing 
and drive strength.
 * **PADS_BANK0** mapped at 0x4001C000 responsible for electrical 
pad properties.
 * **SIO** mapped at 0xD0000000 responsible for logic levels and 
direction when the pin is in sio function.

## Implementation Details

### Function Selection (IO_BANK0)

Each GPIO pin has a control register in IO_BANK0, bits[4:0] of 
this register determine the function. This block determines 
what is driving the pin. A physical pin can be connected to the
different hardware, including SIO(Software Control). Each pin has
two 32-bit registers in this bank (STATUS and CTRL).

### Electrical Properties (PADS_BANK0)

PADS_BANK0 controls the physical electrical characteristics of the pad.
This includes Pull-up/Pull-down resistors and the input enable.
Stride is 32 bits per pin, the array starts at base, the first register 
is VOLTAGE_SELECT, the register for pin 0 is at offset 0x04.

### Levels and Direction (SIO)

The SIO peripheral is a collection of 32-bit registers, where each 
register performs a unique operation, for example setting levels or 
clearing direction. Within every register every GPIO pin is mapped 
to one bit. SIO is connected directly to IOPORT, which allows for fast
and deterministic toggling of pins.

## Practical Application
The driver includes an example of usage, that implements multiple
toggle states using two physical switches to cycle between four distinct
LED blinking frequencies.

## References
 * RP2040 Datasheet A microcontroller by Raspberry Pi
 * Raspberry Pi Documentation Pico C SDK

