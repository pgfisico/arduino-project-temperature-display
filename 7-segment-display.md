# 7-Segment Display

> #### Warning::ESD Sensitive
>
> The 7-segment displays are ESD sensitive

The display will be made up of two 7-segment displays. The displays you will use in this project look similar to displays you may have seen in clocks or other electronic devices. With two 7-segment displays, your display will be capable of displaying two digits.

The image below from the display's datasheets shows each segment of the display in the bottom right. The display has seven segments, labelled A through G, as well as a decimal point, labelled DP. Using two displays, you will be able to display two digit temperature readings.

![](/assets/datasheet/INND-TS56YGCB_Pinout.png)

Each segment of the display has it's own LED to illuminate it. The display you are using in this project has a **common cathode**, meaning the cathode of all the LEDs in the display are connected together. This is shown on the right side of the circuit diagram from the display's datasheet below. Both pin 3 and 8 are connected to the cathodes of all the LEDs. The figure also shows which pin number is connected to the anode of the LED for each segment of the display.

![](/assets/datasheet/INND-TS56YGCB_Internal-Circuit.png)

To display numbers on the display, you need to light up the correct segments to make the number. Since you are using a common cathode display, you must turn on the appropriate LEDs by connecting their anode to power and the common cathode to ground \(with current limiting resistors on each anode, of course\). For example, to display a zero, you must illuminate the segments A, B, C, D, E, and F. You do this by connecting pins 7, 6, 4, 2, 1, and 9 to power.
