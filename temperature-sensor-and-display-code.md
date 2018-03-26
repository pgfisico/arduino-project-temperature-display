# Temperature Sensor and Display Code

Create a new sketch in the Arduino IDE, and replace all the code in the sketch with the code from below.

> #### Note::Incomplete Code
>
> This code is incomplete. You will need to make changes to it so that it works correctly. The changes you need to make are marked with comments starting with `TODO`, and are described in more detail below.

```c
const int TEMPERATURE_ANALOG_PIN = 0;

const int SERIAL_DATA_PIN = 3;
const int SHIFT_CLEAR_PIN = 4;
const int SHIFT_CLOCK_PIN = 5;
const int STORAGE_CLOCK_PIN = 6;

const float ZERO_DEGREE_CELSIUS_MILLIVOLTS = 400;
const float MILLIVOLTS_PER_DEGREE_CELSIUS = 19.5;

const int DELAY_MILLISECONDS = 1000;

// TODO Set the values in this lookup table correctly to display each number
const byte digits[10] = {
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000,
  B00000000
};

void setup() {
  pinMode(SERIAL_DATA_PIN, OUTPUT);
  pinMode(SHIFT_CLEAR_PIN, OUTPUT);
  pinMode(SHIFT_CLOCK_PIN, OUTPUT);
  pinMode(STORAGE_CLOCK_PIN, OUTPUT);

  // Clear the shift register
  digitalWrite(SHIFT_CLEAR_PIN, LOW);
  digitalWrite(SHIFT_CLEAR_PIN, HIGH);
  digitalWrite(STORAGE_CLOCK_PIN, LOW);
  digitalWrite(STORAGE_CLOCK_PIN, HIGH);
}

void loop() {
  int adcValue = analogRead(TEMPERATURE_ANALOG_PIN);

  float millivolts = (((float) adcValue * 5) / 1024) * 1000;
  float temperature = (millivolts - ZERO_DEGREE_CELSIUS_MILLIVOLTS) / MILLIVOLTS_PER_DEGREE_CELSIUS;
  int roundedTemperature = round(temperature);

  int tensDigit = constrain(roundedTemperature / 10, 0, 9);
  int onesDigit = constrain(roundedTemperature % 10, 0, 9);

  // TODO Set STORAGE_CLOCK_PIN LOW

  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, digits[onesDigit]);
  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, digits[tensDigit]);

  // TODO Set STORAGE_CLOCK_PIN HIGH

  delay(DELAY_MILLISECONDS);
}
```

There are a few different sections in the code above, and there a few things you need to change to get it to work correctly. All these items are described in the following sections.

## Constants

All the code before the `setup()` function defines several constants. You have already seen the first constant, `TEMPERATURE_ANALOG_PIN`, in the temperature sensor code. The next several constants are for the pin numbers that the shift register is connected to. Each constant's name indicates the function of the pin on the shift register.

After the pin constants, there is constants for the temperature calculation and the delay between measurements. You have also already seen these in the temperature sensor code.

The next item, named `digits`, is an array with 10 entries (which is why it's written as `digits[10]`. The values in the array will be sent to the shift register to light up the right segments on the display. The `digits` array also has the type `byte` The type `byte` is similar to `int`, except `byte` is smaller than `int`, and cannot represent as many values.

Microcontrollers like the one on the Arduino board (as well as many other electronic devices, such as the processor in a computer) work with binary numbers. Binary numbers are base-two, meaning every digit in a binary number is either 0 or 1. This is different from the numbers you normally use, which are base-ten, where every digit can have one of ten possible values from 0 to 9. A bit, or binary digit, must be either 0 or 1, and a byte is made up of eight bits. The `byte` type used for the `digits` array is therefore made up of eight bits.

You may have heard that computers use "ones and zeros", and this is true, since computers work with binary values. Binary values, however, do not always have to be ones and zeros. Binary values can be whatever you decide they represent, for example instead of 0 and 1, a bit could represent false and true, off and on, low and high, empty and full, or sad and happy. As you put more and more bits together, you can represent increasingly complex things. Interpreting binary values is foundational to how computers operate. Computers are able to perform complex tasks which are all built up from the two possible values of a single bit.

Inside the `digits` array, there are ten entries, each one a byte in size. These entries are the values that start with `B` and end with a comma. The array has been spaced out so that each entry is on a separate line. The `B` tells the Arduino IDE that you are going to specify the value of the entry in binary. Currently, every entry in `digits` is a byte where all the bits are zero. This is specified by the eight zeros after the `B` (recall that a byte has eight bits). A byte where all the bits are one could be written as `B11111111`.

As mentioned earlier, the values in `digits` will be sent to the shift register to light up the display. A 7-segment display has eight different LEDs which can be turned on. These LEDs are used to light up the seven different segments and the decimal point. Whether the eight different LEDs in the display are on or off can be perfectly represented with a single byte (which is made up of eight bits). A zero will represent an LED that is off and a one will represent an LED that is on. The byte that represents which LEDs are turned on or off can then be communicated to a shift register. The byte can be sent serially to the shift register, one bit at a time, by using a low voltage for bits that are zero and a high voltage for bits that are one. Since bits that are zero are represented using a low voltage, the shift register output corresponding to that bit will not provide any power, meaning the LED in the display connected to that output will be off. Similarly, for bits that are one, the corresponding output on the shift register will have a high voltage and provide power, turning on the LED in the display that is connected to that output.

To represent which LEDs are on and off for each digit from 0 to 9, you would need ten bytes. This is why the `digits` array has ten entries. Each entry corresponds to a single digit. The first entry in `digits` is the byte to send to the shift register so the attached display shows a zero. On the next line, the second entry in `digits` is the byte to send to the shift register so the attached display shows a one. The entries continue in this order until the last entry, which is the byte to send to the shift register so the attached display shows a nine. Using an array in this way is commonly known as a _lookup table_, since you look up a specific entry in the array using a _key_. In the case of arrays, the key used to lookup a value is the position of the entry in the array. The first entry in an array is at position 0 (not 1), the second entry is at position 1, the third entry is at position 2, and so on. This means that the order of the bytes an the array, where the fist byte is for the digit 0, the second byte for the digit 1, and the last byte for the digit 9, matches exactly with the key values needed to lookup the bytes in the array, where the key values are 0 for the first entry, 1 for the second entry, and 9 for the last entry. This makes it easy to look up values to send them to the shift register.

The display circuit you built earlier is designed so that each bit in the entries of `digits` corresponds to the segment shown in the table below. The names of the segments are the same as the names used in the display's datasheet, which is also shown below. For example, the byte shown in the table (`01101100`) would turn on the segments B, C, E, and F on the display when sent to the shift register.

![](/assets/Lookup-Table.png)

![](/assets/datasheet/INND-TS56YGCB_Pinout.png)

The bytes in `digits` are currently made up of all zero bits. **Using the portion of the 7-segment display datasheet and chart above, update the entries in `digits` so they light up the correct segments for each digit when sent to the shift register.** If you get stuck try looking at the hints or answer below.

**Hint #1**
{% reveal text="Click to reveal Hint #1" %}
The byte `11111100` would light up all the segments required to display a zero. It lights up the segments A, B, C, D, E, and F and leaves segment G and the decimal point (DP) off. Remember to keep the `B` in front of the value and the comma after the entry when updating the value into `digits`
{% endreveal %}

**Hint #2**
{% reveal text="Click to reveal Hint #2" %}
The byte `01100000` would light up all the segments required to display a one.
{% endreveal %}

**Hint #3**
{% reveal text="Click to reveal Hint #3" %}
The byte `11011010` would light up all the segments required to display a two.
{% endreveal %}

**Answer**
{% reveal text="Click to reveal Answer" %}
```c
const byte digits[10] = {
  B11111100,
  B01100000,
  B11011010,
  B11110010,
  B01100110,
  B10110110,
  B10111110,
  B11100000,
  B11111110,
  B11110110
};
```
{% endreveal %}

## Setup
The code in the `setup()` function sets all the pins used for the shift register as outputs. It then clears the shift register to ensure all the outputs are at a low voltage, which makes the display blank. The clearing of the shift register is accomplished by setting the `SHIFT_CLEAR_PIN` low (since it is an _active low_ signal). It then must be set back to high, otherwise the shift register will constantly be cleared, and will never store any data. The `SHIFT_CLEAR_PIN` only clears the shift register, but does not clear the storage register. To clear the storage register, the code creates a positive edge on the `STORAGE_CLOCK_PIN` by setting it low and then high. This positive edge (low to high transition) causes the now cleared shift register to be copied to the storage register. Since the storage register is also cleared, the outputs from the shift register will now all be at a low voltage, and the displays will be off.

## Loop
The first thing the loop function does is calculate a rounded temperature based on the voltage from the temperature sensor output. This is the same code that you used earlier for the temperature sensor.

Next the program separates the `roundedTemperature` into the `tensDigit` and `onesDigit`. The `tensDigit` is obtained by dividing the `roundedTemperature` by 10. Since both `roundedTemperature` and 10 are integers, `roundedTemperature / 10` performs integer division. This means that it only considers whole multiples. For example, `25 / 10` would produce the result `2`. This is because 10 only fits completely into 25, 2 times. Going back to mathematics, you could also say that 25 divided by 10 is 2, with a remainder of 5. Note that the quotient (2) is the digit in the ten's place and the remainder (5) is the digit in the one's place. The `onesDigit` is obtained by getting the remainder of dividing `roundedTemperature` by 10. This is represented by the `%` (known as the _modulo operator_) in the code `roundedTemperature % 10`. The modulo operator calculates the remainder that is leftover after performing integer division with two numbers.

The calculation of both the `tensDigit` and `onesDigit` also involves the `constrain()` function. The `constrain()` function makes sure that a number is in a certain range, In this case, we ask the constrain function to make sure that the two digits are between 0 and 9, inclusive (i.e. the digits must be greater than or equal to zero, and less than or equal to 9). If the digits are too small, they are replaced with the lowest value in the range (0). If the digits are too large, they are replaced with the highest value in the range (9). This is necessary to prevent the program from using entries that don't exist in the `digits` lookup table. If it tried to use entries that didn't exist, the program would behave unexpectedly.

Ignoring the TODO item for now, the two calls to the `shiftOut()` function send the data to the shift register. The `shiftOut()` function needs to know which pin to use to send the data serially to the shift register (`SERIAL_DATA_PIN`) and which pin it should generate a clock signal on (`SHIFT_CLOCK_PIN`) so the shift register shifts the data in. It also needs to know what order to send the data in (`LSBFIRST`) and what byte of data to send. `LSBFIRST` means _least significant bit_ first, so the Arduino sends the rightmost bit to the shift register first. For example with the byte `11110000`, `LSBFIRST` means the Arduino sends the zeros to the shift register before the ones. The byte to send is looked up in the `digits` lookup table you completed earlier. This is done by `digits[onesDigit]` and `digits[tensDigit]` in the code. The key to look up in the table is in between the square brackets after `digits`. As explained before, the entries in `digits` are ordered so that they key used to look them up is the number you want to display. Note that the `onesDigit` is sent to the shift register before the `tensDigit`. This is because, as explained earlier, the `onesDigit` should be displayed on the rightmost display. The rightmost display is connected to the second shift register, so the data must shift through the first shift register before getting to the second shift register. Sending the `onesDigit` first shifts it into the first shift register, and sending the `tensDigit` next shifts the `onesDigit` out of the first shift register and into the second shift register, and shifts the `tensDigit` into the first shift register.

Returning to the TODO item, recall from earlier that the shift register you are using has both a shift register and a storage register. To actually see the output on the display, you must copy the data from the shift register to the storage register. This is accomplished by creating a positive edge (low to high transition) on the `STORAGE_CLOCK_PIN`. To create the positive edge, before calling the the `shiftOut()` function, the `STORAGE_CLOCK_PIN` should be set `LOW`. After the calls to the `shiftOut()` function the data has been sent to the shift register, and the `STORAGE_CLOCK_PIN` should be set `HIGH`. **Update the code so that before sending data to the shift register, `STORAGE_CLOCK_PIN` is set `LOW`. After sending data to the shift register, set `STORAGE_CLOCK_PIN` `HIGH`**. If you get stuck try looking at the hint or answer below.

**Hint**
{% reveal text="Click to reveal Hint" %}
`digitalWrite(STORAGE_CLOCK_PIN, LOW);` sets the `STORAGE_CLOCK_PIN` `LOW`.
{% endreveal %}

**Answer**
{% reveal text="Click to reveal Answer" %}
```c
// TODO Set STORAGE_CLOCK_PIN LOW
digitalWrite(STORAGE_CLOCK_PIN, LOW);
```
```c
// TODO Set STORAGE_CLOCK_PIN HIGH
digitalWrite(STORAGE_CLOCK_PIN, HIGH);
```

{% endreveal %}

At the end of the `loop()` function, the `delay()` function is used so that the program waits before measuring the temperature again.
