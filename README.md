# ⌚ Smartwatch IoT Project

## 🌟 Overview
The **Smartwatch IoT Project** is an innovative wearable device designed to monitor the user's body temperature. If the temperature exceeds the normal range, the smartwatch triggers a buzzer alert to notify the user. This project was developed using **Tinkercad** and aims to showcase the potential of IoT in enhancing personal health monitoring.

---

## 🛠️ Features
- 🌡️ **Temperature Monitoring:** Tracks the user's body temperature in real time using the TMP36 temperature sensor.
- 🚨 **Buzzer Alert:** Activates a buzzer if the temperature exceeds the preset normal range.
- 💡 **User-Friendly Design:** A simple and efficient solution for wearable health monitoring.
- ⚙️ **IoT Integration:** Demonstrates the use of IoT sensors and components in a practical application.

---

## 🔧 Components Used
1. **TMP36 Temperature Sensor** - Measures the body temperature.
2. **Arduino Uno** - Microcontroller for processing the sensor data.
3. **Buzzer** - Alerts the user when the temperature exceeds normal levels.
4. **Resistors & Wires** - Basic electronic components for the circuit.
5. **Breadboard** - For prototyping and connecting components.

---

## 🖥️ Software Tools
- **Tinkercad** - Used to design and simulate the circuit.
- **Arduino IDE** - For writing and uploading the code (optional if moving from simulation to hardware).

---

## 🚀 How It Works
1. The **TMP36 sensor** continuously measures the body temperature and sends the data to the **Arduino Uno**.
2. The microcontroller processes the sensor data.
3. If the temperature exceeds the normal threshold (e.g., 37°C or 98.6°F), the **buzzer** is triggered to alert the user.
4. The system resets and continues monitoring after the alert.

---

## 🔌 Circuit Design
### Schematic
1. Connect the **VCC** and **GND** pins of the TMP36 sensor to the Arduino's **5V** and **GND**, respectively.
2. Connect the **analog output** of the TMP36 to one of the Arduino's **analog pins (e.g., A0)**.
3. Attach the **buzzer** to one of the Arduino's **digital pins (e.g., D9)**, with a resistor in series.
4. Use the **breadboard** to neatly organize and connect all components.

---

## 📝 Arduino Code Example
Here’s a sample code snippet for the project:

```c
#include <Blynk.h>
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>
#include "Adafruit_GFX.h"
#include "OakOLED.h"

#define REPORTING_PERIOD_MS 1000
OakOLED oled;

char auth[] = "xxxxx-xxxx-xxx";             // Authentication Token Sent by Blynk
char ssid[] = "xxxxx-xxxx-xxx";        //WiFi SSID
char pass[] = "xxxxx-xxxx-xxx";        //WiFi Password

// Connections : SCL PIN - D1 , SDA PIN - D2 , INT PIN - D0
PulseOximeter pox;

float BPM, SpO2;
uint32_t tsLastReport = 0;

const unsigned char bitmap [] PROGMEM =
{
  0x00, 0x00, 0x00, 0x00, 0x01, 0x80, 0x18, 0x00, 0x0f, 0xe0, 0x7f, 0x00, 0x3f, 0xf9, 0xff, 0xc0,
  0x7f, 0xf9, 0xff, 0xc0, 0x7f, 0xff, 0xff, 0xe0, 0x7f, 0xff, 0xff, 0xe0, 0xff, 0xff, 0xff, 0xf0,
  0xff, 0xf7, 0xff, 0xf0, 0xff, 0xe7, 0xff, 0xf0, 0xff, 0xe7, 0xff, 0xf0, 0x7f, 0xdb, 0xff, 0xe0,
  0x7f, 0x9b, 0xff, 0xe0, 0x00, 0x3b, 0xc0, 0x00, 0x3f, 0xf9, 0x9f, 0xc0, 0x3f, 0xfd, 0xbf, 0xc0,
  0x1f, 0xfd, 0xbf, 0x80, 0x0f, 0xfd, 0x7f, 0x00, 0x07, 0xfe, 0x7e, 0x00, 0x03, 0xfe, 0xfc, 0x00,
  0x01, 0xff, 0xf8, 0x00, 0x00, 0xff, 0xf0, 0x00, 0x00, 0x7f, 0xe0, 0x00, 0x00, 0x3f, 0xc0, 0x00,
  0x00, 0x0f, 0x00, 0x00, 0x00, 0x06, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00
};

void onBeatDetected()
{
  Serial.println("Beat Detected!");
  oled.drawBitmap( 60, 20, bitmap, 28, 28, 1);
  oled.display();
}

void setup()
{
  Serial.begin(115200);
  oled.begin();
  oled.clearDisplay();
  oled.setTextSize(1);
  oled.setTextColor(1);
  oled.setCursor(0, 0);

  oled.println("Initializing pulse oximeter..");
  oled.display();

  pinMode(16, OUTPUT);
  Blynk.begin(auth, ssid, pass);

  Serial.print("Initializing Pulse Oximeter..");

  if (!pox.begin())
  {
    Serial.println("FAILED");
    oled.clearDisplay();
    oled.setTextSize(1);
    oled.setTextColor(1);
    oled.setCursor(0, 0);
    oled.println("FAILED");
    oled.display();
    for (;;);
  }
  else
  {
    oled.clearDisplay();
    oled.setTextSize(1);
    oled.setTextColor(1);
    oled.setCursor(0, 0);
    oled.println("SUCCESS");
    oled.display();
    Serial.println("SUCCESS");
    pox.setOnBeatDetectedCallback(onBeatDetected);
  }

