# Display Test

Create a new sketch in the Arduino IDE, and replace all the code in the sketch with the code from below. Don't worry if you don't understand how this code works; it is intended to be a quick test to make sure your display is wired correctly. The code will be explained later when you complete a combined program to display the temperature on the 7-segment displays.

---

# TODO

* Add demo of what a successful test looks like

---

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
    shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, b);
    
    digitalWrite(STORAGE_CLOCK_PIN, HIGH);

    b >>= 1;
    delay(DELAY_MILLISECONDS);
  }

  // Test blank
  digitalWrite(STORAGE_CLOCK_PIN, LOW);

  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, b);
  shiftOut(SERIAL_DATA_PIN, SHIFT_CLOCK_PIN, LSBFIRST, b);

  digitalWrite(STORAGE_CLOCK_PIN, HIGH);

  delay(DELAY_MILLISECONDS);
}
```

Connect the Arduino board to the computer and upload the program. The program will turn on one segment at a time on both displays (meaning the two displays should never display different things) to make sure they are wired correctly. It leaves the segment on for one second before testing the next segment.

The program tests the segments repeatedly in the following order: A, B, C, D, E, F, G, DP, and then it makes the display blank. Refer to the diagram in the bottom right from the display's datasheet to identify which segment is which. **Verify that the segments on both displays light up in the correct order.**

![](/assets/datasheet/INND-TS56YGCB_Pinout.png)

> #### Success::The Display Works!
>
> If the display segments light up in the correct order, the display is working.
>
> If the display segments aren't lighting up in the right order, double check your circuit. If you can't figure out the problem, ask a mentor for help.