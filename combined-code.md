# Combined Code

---

# TODO
- Have students generate LUT
- Have students wrap shiftOut with SRCLK strobe

```c
const int TEMPERATURE_ANALOG_PIN = 0;

const int SERIAL_DATA_PIN = 3;
const int SHIFT_CLEAR_PIN = 4;
const int SHIFT_CLOCK_PIN = 5;
const int STORAGE_CLOCK_PIN = 6;

const float ZERO_DEGREE_CELSIUS_MILLIVOLTS = 400;
const float MILLIVOLTS_PER_DEGREE_CELSIUS = 19.5;

const int DELAY_MILLISECONDS = 1000;

const byte digits[] = {
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



