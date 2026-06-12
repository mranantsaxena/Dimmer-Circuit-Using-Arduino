# 💡 Arduino Dimmer Circuit

Arduino-based dimmer using a 250kΩ potentiometer and 100Ω resistor.
Rotate the knob to smoothly control bulb/LED brightness via PWM.

Simulation Link : https://www.tinkercad.com/things/976ZdMjROhz-dimmer-circuit?sharecode=24Pi5XD-f4Zx0cpobbzgsff_9fQnxEGxQNhdBrWm7mo

## 🧰 Components

| Component | Value |
|---|---|
| Microcontroller | Arduino UNO |
| Potentiometer | 250 kΩ |
| Resistor | 100 Ω |
| Output | Bulb / LED |

## 🔌 Wiring

| Component | Pin |
|---|---|
| Potentiometer left leg | GND |
| Potentiometer middle (wiper) | A0 |
| Potentiometer right leg | 5V |
| Bulb/LED + (via 100Ω resistor) | Pin ~9 |
| Bulb/LED – | GND |

## 📄 Code

```cpp
const int POT_PIN      = A0;
const int BULB_PWM_PIN = 9;

const int SMOOTH_SAMPLES = 5;

int previousBrightness = -1;

void setup() {
  pinMode(BULB_PWM_PIN, OUTPUT);
  Serial.begin(9600);
}

int smoothRead(int pin, int samples) {
  long total = 0;
  for (int i = 0; i < samples; i++) {
    total += analogRead(pin);
    delay(2);
  }
  return (int)(total / samples);
}

void loop() {
  int potValue   = smoothRead(POT_PIN, SMOOTH_SAMPLES);
  int brightness = map(potValue, 0, 1023, 0, 255);
  brightness     = constrain(brightness, 0, 255);

  if (brightness != previousBrightness) {
    analogWrite(BULB_PWM_PIN, brightness);

    Serial.print("Pot (A0): ");
    Serial.print(potValue);
    Serial.print("  |  PWM (Pin 9): ");
    Serial.print(brightness);
    Serial.print("  |  Brightness: ");
    Serial.print(map(brightness, 0, 255, 0, 100));
    Serial.println("%");

    previousBrightness = brightness;
  }

  delay(10);
}
```
## ⚙️ How It Works

- Potentiometer acts as a voltage divider on pin **A0**
- `analogRead()` returns 0–1023 based on knob position
- `map()` converts that to 0–255 PWM range
- `analogWrite()` sends PWM signal to pin **~9** to dim the bulb
- Smoothing (5-sample average) eliminates flicker from the 250kΩ pot


## 🖥️ Serial Monitor

Open Serial Monitor at **9600 baud** to see live output:

```
Pot (A0): 512  |  PWM (Pin 9): 127  |  Brightness: 49%
```
