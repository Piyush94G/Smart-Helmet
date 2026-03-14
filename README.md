# ⛑️ Smart Safety Helmet for Miners & Industrial Workers

> *"Ensuring safety through innovation — turning real-time data into life-saving alerts."*

An IoT-based wearable safety system that integrates multiple sensors into a standard hard hat to continuously monitor hazardous environmental and health conditions for miners and industrial workers.

---

## 📹 Demo Video

[![Smart Safety Helmet Demo](https://img.youtube.com/vi/WrGliUR9ckw/maxresdefault.jpg)](https://www.youtube.com/watch?v=WrGliUR9ckw)

> Click the thumbnail above to watch the full project demo on YouTube.

---

## 📋 Table of Contents

- [Demo Video](#demo-video)
- [Overview](#overview)
- [Features](#features)
- [Hardware Components](#hardware-components)
- [Software & Libraries](#software--libraries)
- [Circuit Connections](#circuit-connections)
- [How It Works](#how-it-works)
- [Installation & Setup](#installation--setup)
- [Code Structure](#code-structure)
- [Preliminary Results](#preliminary-results)
- [Future Enhancements](#future-enhancements)
- [Team](#team)

---

## 🧭 Overview

Industrial workers and miners are frequently exposed to life-threatening hazards — toxic gas leaks, extreme temperatures, falls, and poor visibility. Traditional helmets offer only physical protection with no real-time monitoring.

The **Smart Safety Helmet** is an ESP32-powered wearable that:
- Continuously monitors gas levels, temperature, motion, and ambient light
- Triggers immediate visual (LED) and auditory (buzzer) alerts
- Activates a fan automatically during gas/heat events
- Sends fall detection alerts to emergency contacts via **WhatsApp API**
- Supports remote Wi-Fi configuration through **WiFiManager**

---

## ✅ Features

| Feature | Sensor Used | Alert |
|---|---|---|
| Gas / Smoke Detection | MQ-2 | Red LED + Fan |
| High Temperature | DHT11 | Yellow LED |
| Fall Detection | MPU6050 | Blue LED + Buzzer |
| Low Light Auto-Lamp | LDR | Headlamp ON |
| Emergency SMS/WhatsApp | ESP32 + HTTP | Remote Alert |
| SIM-based Calling | SIM800H (GSM) | Outgoing/Incoming Call |

---

## 🔧 Hardware Components

| Component | Purpose |
|---|---|
| ESP32 | Main microcontroller (Wi-Fi + BT) |
| MQ-2 Gas Sensor | Detects LPG, methane, CO, smoke |
| DHT11 | Temperature & humidity monitoring |
| MPU6050 | Accelerometer + gyroscope for fall detection |
| LDR | Light-dependent resistor for ambient light |
| SIM800H GSM Module | Cellular calls in areas without Wi-Fi |
| Piezo Buzzer | Audible alarm |
| 3× LEDs (Red/Yellow/Blue) | Visual indicators for each hazard |
| Mini DC Fan | Ventilation when gas/heat detected |
| Li-Ion Battery (3.7V, 3000mAh) | Power supply |
| BMS Module | Battery protection & charging |

---

## 💻 Software & Libraries

- **Arduino IDE** — firmware development
- **Adafruit MPU6050** — fall detection
- **DHT Sensor Library** — temperature & humidity
- **WiFiManager** — captive portal for Wi-Fi configuration
- **ArduinoJson** — config file parsing (SPIFFS)
- **ESP_DoubleResetDetector** — double-reset to enter config mode
- **HTTPClient** — fall alert via WhatsApp API (CallMeBot)
- **SoftwareSerial** — SIM800H GSM communication
- **ThingSpeak** *(optional)* — cloud data visualization

---

## 🔌 Circuit Connections

### ESP32 Pin Mapping

| Component | ESP32 Pin |
|---|---|
| MQ-2 Analog Out (A0) | GPIO 34 |
| DHT11 Data | GPIO 15 |
| MPU6050 SDA | GPIO 2 |
| MPU6050 SCL | GPIO 15 |
| LDR (Analog) | GPIO 25 |
| LED – Gas Alert | GPIO 18 |
| LED – Temp Alert | GPIO 17 |
| LED – Fall Alert | GPIO 16 |
| Buzzer | GPIO 14 |
| Fan | GPIO 13 |
| Headlamp LEDs | GPIO 26 |
| SIM800H RX | GPIO 16 |
| SIM800H TX | GPIO 17 |
| Emergency Button | GPIO 12 |

> ⚠️ Use a 220Ω resistor in series with each LED. Ensure SIM800H is powered by a stable 5V source — it draws high current during transmission.

---

## ⚙️ How It Works

```
START
  │
  ├── Initialize all sensors (MQ-2, DHT11, MPU6050, LDR)
  │
  └── LOOP:
        ├── Read gas level (MQ-2)
        │     └── If > threshold → Gas LED ON + Fan ON
        │
        ├── Read temperature (DHT11)
        │     └── If > 60°C → Temp LED ON
        │
        ├── Read acceleration (MPU6050)
        │     └── If free-fall detected (accel < 8.5 m/s²)
        │           → Fall LED ON + Buzzer ON
        │           └── Confirm impact → Send WhatsApp alert
        │
        ├── Read ambient light (LDR)
        │     └── If < threshold → Headlamp ON
        │
        └── Check button press
              └── Double press → Send "User is safe" message
```

### Fall Detection Logic

1. Calculate **total acceleration magnitude** from X, Y, Z axes
2. If magnitude drops below `fallThreshold` (8.5 m/s²) → free fall detected
3. Wait for impact (magnitude > `impactThreshold` = 12.0 m/s²)
4. Trigger buzzer + LED, send HTTP alert to emergency contact
5. User can cancel false alarm with a button press

---

## 🚀 Installation & Setup

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/smart-safety-helmet.git
cd smart-safety-helmet
```

### 2. Install Arduino Libraries
In Arduino IDE, install these via Library Manager:
- `Adafruit MPU6050`
- `Adafruit Unified Sensor`
- `DHT sensor library` by Adafruit
- `WiFiManager` by tzapu
- `ArduinoJson`
- `ESP_DoubleResetDetector`

### 3. Flash the Firmware
- Select board: **ESP32 Dev Module**
- Upload `main.ino` to your ESP32

### 4. Configure Wi-Fi & Emergency Contact
On first boot (or double-reset), connect to the AP:
- **SSID:** `Fall_detector`
- **Password:** `clock123`

Open the captive portal and enter:
- Your **name**
- Emergency contact's **WhatsApp number**
- Emergency contact's **CallMeBot API key**

> To get a CallMeBot API key, have your emergency contact send a WhatsApp message to CallMeBot and follow the QR code instructions shown in the portal.

---

## 📁 Code Structure

```
smart-safety-helmet/
│
├── main.ino                  # Main integrated firmware
├── fall_detection.ino        # MPU6050 standalone test
├── gas_sensor.ino            # MQ-2 standalone test
├── temperature_sensor.ino    # DHT11 standalone test
├── ldr_light_control.ino     # LDR + auto headlamp
├── buzzer_led_test.ino       # Component test
├── gsm_call.ino              # SIM800H calling module
└── README.md
```

---

## 📊 Preliminary Results

We have successfully integrated and tested the following:

- ✅ **MQ-2 Gas Sensor** — detects gas concentration changes, triggers LED + fan above threshold
- ✅ **DHT11** — real-time temperature & humidity readings
- ✅ **MPU6050** — fall detection via acceleration magnitude with buzzer confirmation
- ✅ **LDR** — auto headlamp activation in low-light conditions
- ✅ **Buzzer + 3 LEDs** — visual/auditory alert system fully functional
- ✅ **WiFiManager** — Wi-Fi setup via captive portal
- ✅ **WhatsApp Alert** — fall detection alert sent to emergency contact via HTTP

**LED Status Key:**
| LED Color | Condition |
|---|---|
| 🟢 Green (Headlamp) | Low light detected |
| 🔴 Red | Gas level exceeded |
| 🟡 Yellow | High temperature |
| 🔵 Blue | Fall detected |

---

## 🔮 Future Enhancements

- [ ] MAX30102 Pulse Oximeter for heart rate & SpO₂ monitoring
- [ ] OLED display for real-time on-helmet data readout
- [ ] LoRa module for communication in underground areas without Wi-Fi
- [ ] Solar panel integration for extended battery life
- [ ] Dust & radiation sensor support
- [ ] ThingSpeak cloud dashboard for supervisor monitoring
- [ ] Mobile app for real-time worker tracking

---

> ⛑️ *Built with care for the workers who keep industries running safely.*
