# Display Code

---

# TODO

-- Consider making this display test page, don't explain how the code works. Cover in the Combined code with the LUT

```c
const int SERIAL_DATA_PIN = 3;
const int SHIFT_CLEAR_PIN = 4;
const int SHIFT_CLOCK_PIN = 5;
const int STORAGE_CLOCK_PIN = 6;

const int DELAY_MILLISECONDS = 1000;

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
  byte b = (1 << 7);

  // Test each segment
  while (b != 0) {
    digitalWrite(STORAGE_CLOCK_PIN, LOW);
    shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, b);
    digitalWrite(STORAGE_CLOCK_PIN, HIGH);

    b >>= 1;
    delay(DELAY_MILLISECONDS);
  }

  // Test blank
  digitalWrite(STORAGE_CLOCK_PIN, LOW);
  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, b);
  digitalWrite(STORAGE_CLOCK_PIN, HIGH);

  delay(DELAY_MILLISECONDS);
}
```



