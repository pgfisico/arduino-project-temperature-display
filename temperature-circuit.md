# Temperature Sensor Circuit

Before building the display, you will connect the temperature sensor to the Arduino and display the temperature on the Serial Monitor in the Arduino IDE.

Disconnect the Arduino board from the computer and construct the circuit pictured on this page. The 5V and ground connections to the bottom of the breadboard will be used later when building the display.

> #### Warning::ESD Sensitive
> The temperature sensor is ESD sensitive

In the circuit pictured below, the flat side of the temperature sensor is facing the empty half of the breadboard. It is important to orient the the temperature sensor this way so that you do not connect the power backwards. With the flat side of the sensor facing you, the pins have the following functions from left to right: power, sensor output, ground. See the pin diagram below from the sensor's datasheet below.

![](/assets/MCP9701A_TO-92_Pinout.PNG)

The blue component behind the temperature sensor is a 0.1&micro;F (0.1 microfarad) capacitor. It should be placed as close as possible to the temperature sensor, and is connected across the power and ground pins of the sensor.

> #### Info::View Original Size
> Click on the circuit pictures to see them at their original size

![](/assets/temperature_sensor_breadboard.png)
[View Original Size](/assets/temperature_sensor_breadboard.png)

## What's a capacitor?

-- What are capacitors
---- Why is it safe to short the voltage rails

> #### Success::Check your Circuit
> Before continuing, check your circuit to confirm it is wired correctly