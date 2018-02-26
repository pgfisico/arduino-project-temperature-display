# Temperature Sensor Code

---

# TODO

```c
const int TEMPERATURE_ANALOG_PIN = 0;

const float ZERO_DEGREE_CELSIUS_MILLIVOLTS = 400;
const float MILLIVOLTS_PER_DEGREE_CELSIUS = 19.5;

void setup() {
  Serial.begin(9600);
}

void loop() {
  int adcValue = analogRead(TEMPERATURE_ANALOG_PIN);

  float millivolts = (((float) adcValue * 5) / 1024) * 1000;
  float temperature = (millivolts - ZERO_DEGREE_CELSIUS_MILLIVOLTS) / MILLIVOLTS_PER_DEGREE_CELSIUS;
  int roundedTemperature = round(temperature);

  Serial.println(roundedTemperature);
}
```



