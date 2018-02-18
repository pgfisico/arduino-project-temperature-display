# Display Circuit

You will now build the display, so that the temperature can be displayed on it rather than the computer. The display will be made up of the following components.

> #### Warning::ESD Sensitive
> The 7-segment displays and shift registers are ESD sensitive

## 7-Segment Display

The display will be made up of two 7-segment displays. The displays you will use in this project look similar to displays you may have seen in clocks or other electronic devices.

The image below from the display's datasheets shows each segment of the display in the bottom right. The display has seven segments, labelled A through G, as well as a decimal point, labelled DP. Using two displays, you will be able to display two digit temperature readings.

![](/assets/INND-TS56YGCB_Pinout.PNG)

Each segment of the display has it's own LED to illuminate it. The display you are using in this project has a common cathode, meaning the cathode of all the LEDs in the display are connected together. This is shown on the right side of the circuit diagram from the display's datasheet below. Both pin 3 and 8 are connected to the cathodes of all the LEDs. The figure also shows which pin number is connected to the anode of the LED for each segment of the display.

![](/assets/INND-TS56YGCB_Internal-Circuit.PNG)

To display numbers on the display, you need to light up the correct segments to make the number. Since you are using a common cathode display, you must turn on the appropriate LEDs by connecting their anode to power and the common cathode to ground \(with current limiting resistors on each anode, of course\). For example, to display a zero, you must illuminate the segments A, B, C, D, E, and F. You do this by connecting pins 7, 6, 4, 2, 1, and 9 to power.

## Shift Register

To control every segment on your two digit display, you would need 16 digital outputs. While it is possible to connect the displays directly to the Arduino, this will use up most of the pins. If you were building something with several components in addition to the display, there would be very few pins left to connect other components to. You may also have other limitations related to power, timing, or other factors that would make connecting all the individual display segments to the Arduino a bad choice. To make this project different from controlling LEDs with the Arduino and to introduce you to digital logic, you will use a shift register to control each display. Using shift registers also allows you to add more digits to your display, since there are not enough pins on the Arduino UNO to control three or more of the 7-segment displays.

### Serial and Parallel Communication

The shift register you will use for this project is known as a _serial-in, parallel-out \(SIPO\) shift register_. This means that you provide input data serially, and the shift register outputs in parallel. **The meaning of serial and parallel is different in this context than in electrical circuits!** To understand what serial and parallel mean in this context, say you want to send a message from one point to another using electrical wiring. The message will be represented as a sequence of high and low voltages on the wire \(for example, a high could be 5 volts and a low could be 0 volts\). You will be sending the message `HLLHHLHL`, where H represents a high voltage and L represents a low voltage

If you were to transmit this message serially, you could use a single piece of wire \(You would also need a common reference point between the sender and receiver so that you can measure voltage correctly at the receiver\). You would connect the sending end of the wire to high or low voltage in the order defined by the message. In the case of the message `HLLHHLHL`, you would do the following.

1. Connect wire to **high** voltage
2. Wait
3. Connect wire to **low** voltage
4. Wait
5. Leave wire connected to **low** voltage
6. Wait
7. Connect wire to **high** voltage
8. Wait
9. Leave wire connected to **high** voltage
10. Wait
11. Connect wire to **low** voltage
12. Wait
13. Connect wire to **high** voltage
14. Wait
15. Connect wire to **low** voltage
16. Wait

The time spent waiting is to provide the enough time to measure the voltage at the receiving end of the wire.

If you were to transmit this message in parallel, you could use eight pieces of wire \(Again you would also need a reference point for voltage measurements\). You would connect the wires at the sending end as follows, and wait a bit for the voltage to be measured at the receiving ends of the wires.

| **Wire Number** | **Voltage** |
| :--- | :--- |
| 1 | High |
| 2 | Low |
| 3 | Low |
| 4 | High |
| 5 | High |
| 6 | Low |
| 7 | High |
| 8 | Low |

> #### Info::Universal Serial Bus
> The Universal Serial Bus \(USB\) transmits data serially. It does however use two wires for _differential signaling_ instead of a single wire to protect the signals from interference. This is important when the signals travel over long cables that are near other cables.

Returning to the shift register you will use to control the displays, you might be able to guess what a serial-in, parallel-out shift register is now that you know what serial and parallel communication is. The shift register uses a single wire to receive data and outputs multiple pieces of data in parallel. The shift register you are using is an 8-bit shift register, so it has 8 parallel outputs. This is enough outputs to control all the segments on a single display.

The shift register is called a shift register because it shifts the single serial input through each of the parallel outputs, similar to how a message is passed between people playing telephone. This is shown in the diagram below, where the message from the previous example is sent to the shift register. Initially, the shift register is outputting the values in gray at time 0. At time 0, the serial input is also set to a low voltage. At time 1, the red serial input is shifted into output 1. At time 1, the serial input is also changed to a high voltage. At time 2, the orange serial input is shifted into output 1, and the red input is shifted into output 2. This continues until all eight outputs have the desired high or low voltage. Although not shown in the diagram, the shift register also has a serial output that will have the previous value of output 8 every time a new input is shifted in. This output allows you to connect multiple shift registers together, expanding the number of outputs while continuing to only use the single serial input.

![](/assets/SIPO-Shift-Register.png)

The shift register you are using in this project uses the power supply voltage as a high voltage (in this case, 5 volts), and ground as a low voltage (recall ground is the reference point for voltage, so it has the value of 0 volts). Since the grounds of the Arduino and the shift register are connected together, both the Arduino and shift register have a common point of reference for 0 volts.

### Clock Signals

After reading the previous section about serial and parallel communication, you might be wondering how you synchronize the transmitter and receiver so that the receiver measures the voltage at the right time. If the receiver was measuring slower than the transmitter was transmitting, it would miss portions of the message. It might also measure while the sender is changing the voltage from low to high or high to low, and could measure the opposite of what the sender intended because the voltage is somewhere between the low and high values. Alternatively, if the receiver is measuring faster than the transmitter is transmitting, it could receive extra parts of the message that the transmitter doesn't intend to send. It could also end up measuring when the voltage is changing as described previously.

To synchronize digital circuits, a clock signal is commonly used. A clock signal is shown in the timing diagram below. Time is shown from left to right. The green line represents the voltage of the clock signal. The horizontal dashed yellow lines indicate the low and high voltage levels. When the green line is at the same level as the bottom yellow line, the clock signal is at the low voltage, and when it is at the same level as the top yellow line, it is at the high voltage.

The shift register you are using in this project is _positive-edge triggered_. This means that the shift register shifts the serial input into the output every time the clock signal changes from low to high. These transitions are marked using white arrows in the timing diagram. There are numerous other specific timing constraints needed for the shift register to operate properly, but you don't need to worry about these. Look up the data sheet for the shift register you're using if you're interested in learning more.

![](/assets/Clock-Signal.png)

> #### Info::Universal Serial Bus
> The Universal Serial Bus \(USB\) does not use a clock signal. Instead, it modifies the serial data signal so that it doesn't stay high or low for too long. This means that changes from low to high or high to low occur frequently enough for the receiver to perform _clock recovery_.

### Storage...????

-- Internal diagram \(storage/shift\)

### Dual In-Line Package

* Notch, pin numbering
  -- OE / Tri-state
  -- Chaining???

-- TIMING --

Disconnect the Arduino board from the computer and construct the circuit pictured on this page.  
_\*_ ADD-ON to existing!!!

\*\* WARN - breaking pins on ICs

-- NOte that caps are reused here - close to IC

> #### Success::Check your Circuit
>
> Before continuing, check your circuit to confirm it is wired correctly
