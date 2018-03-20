# Temperature Sensor and Display Code

Create a new sketch in the Arduino IDE, and replace all the code in the sketch with the code from below.

> #### Note::Incomplete Code
>
> This code is incomplete. You will need to make changes to it so that it works correctly. The changes you need to make are marked with comments starting with `TODO`, and are described in more detail below.

---

# TODO
- Have students generate LUT
- Have students wrap shiftOut with SRCLK strobe

---


```c
const int TEMPERATURE_ANALOG_PIN = 0;

const int SERIAL_DATA_PIN = 3;
const int SHIFT_CLEAR_PIN = 4;
const int SHIFT_CLOCK_PIN = 5;
const int STORAGE_CLOCK_PIN = 6;

const float ZERO_DEGREE_CELSIUS_MILLIVOLTS = 400;
const float MILLIVOLTS_PER_DEGREE_CELSIUS = 19.5;

const int DELAY_MILLISECONDS = 1000;

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

  digitalWrite(STORAGE_CLOCK_PIN, LOW);

  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, digits[onesDigit]);
  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, digits[tensDigit]);

  digitalWrite(STORAGE_CLOCK_PIN, HIGH);

  delay(DELAY_MILLISECONDS);
}
```

There are a few different sections in the code above, and there a few things you need to change to get it to work correctly. All these items are described in the following sections.

## Constants

All the code before the `setup()` function defines several constants. You have already seen the first constant, `TEMPERATURE_ANALOG_PIN`, in the temperature sensor code. The next several constants are for the pin numbers that the shift register is connected to. Each constant's name indicates the function of the pin on the shift register.

After the pin constants, there is constants for the temperature calculation and the delay between measurements. You have also already seen these in the temperature sensor code.

---

# _TODO LUT_

---

## Setup
The code in the `setup()` function sets all the pins used for the shift register as outputs. It then clears the shift register to ensure all the outputs are at a low voltage, which makes the display blank. The clearing of the shift register is accomplished by setting the `SHIFT_CLEAR_PIN` low (since it is an _active low_ signal). It then must be set back to high, otherwise the shift register will constantly be cleared, and will never store any data. The `SHIFT_CLEAR_PIN` only clears the shift register, but does not clear the storage register. To clear the storage register, the code creates a positive edge on the `STORAGE_CLOCK_PIN` by setting it low and then high. This positive edge (low to high transition) causes the now cleared shift register to be copied to the storage register. Since the storage register is also cleared, the outputs from the shift register will now all be at a low voltage, and the displays will be off.

## Loop
The first thing the loop function does is calculate a rounded temperature based on the voltage from the temperature sensor output. This is the same code that you used earlier for the temperature sensor.

Next the program separates the `roundedTemperature` into the `tensDigit` and `onesDigit`. The `tensDigit` is obtained by dividing the `roundedTemperature` by 10. Since both `roundedTemperature` and 10 are integers, `roundedTemperature / 10` performs integer division. This means that it only considers whole multiples. For example, `25 / 10` would produce the result `2`. This is because 10 only fits completely into 25, 2 times. Going back to mathematics, you could also say that 25 divided by 10 is 2, with a remainder of 5. Note that the quotient (2) is the digit in the ten's place and the remainder (5) is the digit in the one's place. The `onesDigit` is obtained by getting the remainder of dividing `roundedTemperature` by 10. This is represented by the `%` (known as the _modulo operator_) in the code `roundedTemperature % 10`. The modulo operator calculates the remainder that is leftover after performing integer division with two numbers.

The calculation of both the `tensDigit` and `onesDigit` also involves the `constrain()` function. The `constrain()` function makes sure that a number is in a certain range, In this case, we ask the constrain function to make sure that the two digits are between 0 and 9, inclusive (i.e. the digits must be greater than or equal to zero, and less than or equal to 9). If the digits are too small, they are replaced with the lowest value in the range (0). If the digits are too large, they are replaced with the highest value in the range (9). This is necessary to prevent the program from using entries that don't exist in the `digits` lookup table. If it did, the program would behave unexpectedly.

---

# _TODO SRCLK_

---


At the end of the `loop()` function, the `delay()` function is used so that the program waits before measuring the temperature again.
