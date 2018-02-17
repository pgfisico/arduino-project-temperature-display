# TITLE

Before building the display, you will connect the temperature sensor to the Arduino and display the temperature on the Serial Monitor in the Arduino IDE.

-- The temperature sensor is ESD sensitive --

Construct the following circuit. The 5V and GND on the lower rail will be used later for the display.

Note that the temperature sensor's pins have the following functions
...
Flat side, L to R

![](/assets/MCP9701A_TO-92_Pinout.PNG)

Capacitor should be placed as close as possible to the temperature sensor.

-- What are capacitors
---- Why is it safe to short the voltage rails

![](/assets/temperature_sensor_breadboard.png)