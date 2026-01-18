# 555-Timer-One-Button-Toggle-Bi-Stable-Switch-with-Status-LED-Common-Anode
A reliable 555 timer bistable circuit that converts a momentary button press into a latching on/off toggle switch with clear visual status indication. Designed as a safety control module for an AVR programmer PCB, this circuit ensures safe microcontroller handling during programming operations.

## The Problem & Application Context

When programming AVR microcontrollers (ATtiny85, ATtiny84, ATtiny4313), improper power sequencing during chip insertion/removal can damage components. This circuit provides:
-    Safe power control: Toggles ground connection (GND to GND_PROG) via MOSFET switching
-    Clear status indication: Red/Blue LED shows current state
-    Single-button operation: User-friendly interface
-    Predictable startup: Always powers up in safe (OFF) state

Circuit Evolution: From Failure to Success

## Attempt 1: Symmetrical Design (Failed)

Initial approach: Complementary NPN/PNP transistors (BC547/BC557) driving both LEDs symmetrically.
***
Concept:
> 555 HIGH → BC547 ON (Red), BC557 OFF (Blue)\
> 555 LOW  → BC547 OFF (Red), BC557 ON (Blue)
***
**Why it failed:**
-    555 output doesn't swing rail-to-rail (3.6V HIGH, 0.7V LOW typical)
-    PNP transistor never fully turns off with 3.6V on its base
-    Result: Both LEDs illuminated simultaneously in all states

## Attempt 2: PNP with Diode Lift (Abandoned)
**Attempted fix:** Add diodes to PNP base to increase turn-off voltage.
***
555 HIGH (3.6V) → Diode (0.7V) → PNP Base = 2.9V\
Still insufficient: 5V - 2.9V = 2.1V across B-E (PNP needs >4.3V to turn off)
***
Why abandoned: Would require 2-3 diodes, adding complexity without guaranteed success.

## Final Solution: Asymmetric Design (Success!)
Key insight: Use the 555's voltage limitations as a feature, not a limitation.
***
Blue LED: Direct connection to 555 output
Red LED: Via NPN transistor from 555 output

When 555 HIGH (3.6V):
- Blue LED: 5V - 3.6V = 1.4V (< Vf_blue) → OFF
- BC547 Base: 3.6V - 0.7V = 2.9V → Transistor ON → Red ON

When 555 LOW (0.017V measured):
- Blue LED: 5V - 0.017V = 4.983V → ON
- BC547 Base: 0.017V → Transistor OFF → Red OFF
***

## Circuit Design Details
Core Components
-    IC1: NE555 Timer (bistable mode)
-    Q1: BC547 NPN Transistor
-    LED1: Common Anode Bi-color LED (Red/Blue)
-    Buttons: SW1 (Toggle)
-    RC Network: 10kΩ + 100nF on pin 4 for power-on reset

## Brightness Matching Discovery
Despite different currents (3.2mA red vs 1.0mA blue), LEDs appear equally bright due to:
-   Human eye sensitivity: ~30% for red (625nm) vs ~10% for blue (470nm)
-   LED efficiency: Blue LEDs typically have lower luminous efficacy
-   Circuit serendipity: The 3:1 current ratio naturally compensates for eye sensitivity

## Power-On Reset Circuit
The 10kΩ resistor and 100nF capacitor on pin 4 (RESET) create a 1ms delay during power-up, ensuring:
-    Circuit always starts in OFF/Blue state
-    No random startup behavior
-    Safe initial condition for the AVR programmer application

## MOSFET Ground Switching
The 555 output controls a MOSFET that switches the programming ground:
***
555 HIGH → MOSFET ON → GND_PROG = GND (Programming enabled, Red LED)\
555 LOW  → MOSFET OFF → GND_PROG floating (Safe to insert chips, Blue LED)
***

## Youtube video
**https://www.youtube.com/watch?v=0-jJqgY_BRs**

**Built and verified on the bench before PCB commitment. Sometimes the simplest solution emerges from understanding component limitations rather than fighting them.**
