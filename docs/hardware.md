# 🛠 Hardware Documentation

> 🇩🇪 [Deutsche Version weiter unten](#-hardware-dokumentation)

---

This document covers the hardware components used in this project, their wiring, and tips for sourcing and assembly.

---

## Bill of Materials (BOM)

| Component | Description | Qty |
| :--- | :--- | :---: |
| ESP32 Dev Board Mini | e.g. ESP32-WROOM-32 (38-pin) | 1 |
| HX711 Load Cell Amplifier | 24-bit ADC for load cells | 1 |
| Load Cell* | 5 or 7 kg, full-bridge | 1 |
| ST7920 LCD*² | 128×64, SPI mode | 1 |
| Rotary Encoder*² | With push-button (KY-040 or similar) | 1 |
| Passive Buzzer*² | 3.3 V compatible | 1 |
| Power Supply | 5 V / 2 A USB or barrel jack | 1 |
<!-- | Potentiometer | 10 kΩ linear, for winder speed | 1 | -->
<!-- | Stepper Motor*² | NEMA 17, for optional winder | 1 | -->
<!-- | Stepper Driver | A4988 or DRV8825 | 1 | -->
<!-- | Filament Runout Sensor*² | Mechanical switch or optical | 1 | -->

> #### **Dimensions for the Load Cell**
> * **Overall Dimensions:** 80 mm x 12.7 mm x 12.7 mm
> * **Mounting Holes:** 4 x M4 or M5 (depending on model)
> * **Hole Spacing (Center to Center):**
>   * **Side A (Fixed Base):** 15 mm
>   * **Side B (Load End):** 15 mm
>   * **Distance between inner holes:** 40 mm

> #### ** *² Parts**
> All parts marked with *² are taken from an old Ender3 or Ender5.  
> If you don't have such a printer left for using the parts, you can get replacement parts pretty cheap on AliExpress for example.
---

## Pinout Overview

All pins are configurable via `substitutions` in `example_config.yaml`. The defaults are:

| Component | Pin | Notes |
| :--- | :--- | :--- |
| **Status LED** | GPIO02 | Built-in LED on most ESP32 boards |
| **Display CLK** | GPIO18 | SPI Clock |
| **Display MOSI** | GPIO23 | SPI Data |
| **Display CS** | GPIO17 | Chip Select |
| **Encoder A** | GPIO32 | Rotary channel A |
| **Encoder B** | GPIO33 | Rotary channel B |
| **Encoder BTN** | GPIO19 | Push-button (active low) |
| **Buzzer** | GPIO16 | PWM output |
| **Load Cell DT** | GPIO21 | HX711 Data (`waegezelle` package) |
| **Load Cell SCK** | GPIO22 | HX711 Clock (`waegezelle` package) |
| **I2C SDA** | GPIO13 | For optional extensions |
| **I2C SCL** | GPIO14 | For optional extensions |
| **Stepper STEP** | GPIO25 | Winder step pulse |
| **Stepper DIR** | GPIO26 | Winder direction |
| **Filament Sensor** | GPIO27 | Runout detection (active low) |
| **Speed (Poti)** | GPIO34 | Analog input (0–3.3 V), input-only pin |
| **Add-on Detection** | GPIO05 | Input with internal pull-up |

> ⚠️ GPIO34–GPIO39 on ESP32 are **input-only** and do not support internal pull-up/pull-down resistors. Use an external 10 kΩ pull-up if needed.

---

## Wiring Diagrams

### Load Cell → HX711 → ESP32

```
Load Cell         HX711          ESP32
─────────         ─────          ─────
RED   (E+)   ──►  E+
BLK   (E-)   ──►  E-
WHT   (A-)   ──►  A-
GRN   (A+)   ──►  A+
                  VCC ──────────►  3.3 V
                  GND ──────────►  GND
                  DT  ──────────►  GPIO21
                  SCK ──────────►  GPIO22
```

> ℹ️ Load cell wire colors may vary by manufacturer. Check the datasheet for your specific cell.

### ST7920 Display → ESP32 (SPI Mode) (from an old Ender3 or Ender5)

```
ST7920            ESP32
──────            ─────
VCC  ──────────►  5 V (or 3.3 V, check your module)
GND  ──────────►  GND
RS   ──────────►  GPIO17 (CS)
RW   ──────────►  GPIO23 (MOSI)
E    ──────────►  GPIO18 (CLK)
PSB  ──────────►  GND    (forces SPI mode)
RST  ──────────►  3.3 V  (or a free GPIO for software reset)
BLA  ──────────►  3.3 V  (backlight anode)
BLK  ──────────►  GND    (backlight cathode)
```

### Stepper Motor → A4988 → ESP32

```
ESP32             A4988          NEMA17
─────             ─────          ──────
GPIO25 ────────►  STEP
GPIO26 ────────►  DIR
3.3 V  ────────►  VDD (logic)
GND    ────────►  GND (logic)
                  VMOT ◄──── 12 V PSU (+)
                  GND  ◄──── 12 V PSU (-)
                  1A/1B ────► Coil A
                  2A/2B ────► Coil B
```

> ⚠️ Set the A4988 current limit before first use to avoid overheating the stepper motor.

---

## Power Supply

The ESP32 and logic components run at **3.3 V** (regulated on-board). The stepper motor requires a separate **12 V supply** connected directly to the stepper driver (VMOT/GND). Do not power the stepper from the ESP32's 3.3 V or 5 V rails.

> *Note:* This section might change - goal is one 12V power-supply for the whole hardware

| Rail | Consumers | Recommended Supply |
| :--- | :--- | :--- |
| 5 V (via USB) | ESP32, HX711, Display, Encoder, Buzzer | USB charger ≥ 1 A |
| 12 V | Stepper motor (winder add-on only) | 12 V / 2 A PSU |

---

## Notes on the Optional Winder Add-on

The winder module connects via a dedicated connector. Its presence is detected via **GPIO05** (pulled high internally; grounded by the add-on connector). When not connected, all winder-related menu items are hidden automatically.

If you are building the winder module, ensure the connector shorts GPIO05 to GND when plugged in.

---
---

# 🛠 Hardware-Dokumentation

Dieses Dokument beschreibt die verwendeten Hardware-Komponenten, ihre Verdrahtung sowie Tipps zur Beschaffung und zum Aufbau.

---

## Stückliste (BOM)

| Komponente | Beschreibung | Menge |
| :--- | :--- | :---: |
| ESP32 Dev Board | z. B. ESP32-WROOM-32 (38-polig) | 1 |
| HX711 Wägezellenmodul | 24-Bit-ADC für Wägezellen | 1 |
| Wägezelle* | 1–7 kg, Halb- oder Vollbrücke | 1 |
| ST7920 LCD*² | 128×64, SPI-Modus | 1 |
| Drehgeber*² | Mit Druckknopf (KY-040 o. ä.) | 1 |
| Passiver Summer*² | 3,3 V kompatibel | 1 |
| Netzteil | 5 V / 2 A USB oder Hohlbuchse | 1 |
<!-- | Potentiometer*² | 10 kΩ linear, für Wicklergeschwindigkeit | 1 | -->
<!-- | Schrittmotor*² | NEMA 17, für optionalen Wickler | 1 | -->
<!-- | Schrittmotortreiber | A4988 oder DRV8825 | 1 | -->
<!-- | Filament-Runout-Sensor*² | Mechanischer Schalter oder optisch | 1 | -->

> #### **Abmessungen für die Wägezelle**
> * **Gesamtmaße:** 80 mm x 12,7 mm x 12,7 mm
> * **Bohrungen:** 4 x M4 oder M5 (je nach Modell)
> * **Lochabstände (Mitte zu Mitte):**
>   * **Seite A (Befestigung):** 15 mm
>   * **Seite B (Lastaufnahme):** 15 mm
>   * **Abstand zwischen den inneren Löchern:** 40 mm

> #### ** *² Teile**
> Alle mit *² gekennzeichneten Komponenten stammen von einem alten Ender3 oder Ender5.
> Sollte kein alter Drucker zum Ausschlachten verfügbar sein, können diese Teile günstig als Ersatzteile bei z.B. AliExpress bezogen werden.
---

## Pinbelegung (Übersicht)

Alle Pins sind über `substitutions` in der `example_config.yaml` konfigurierbar. Standardbelegung:

| Komponente | Pin | Hinweise |
| :--- | :--- | :--- |
| **Status-LED** | GPIO02 | Eingebaute LED auf den meisten ESP32-Boards |
| **Display CLK** | GPIO18 | SPI-Takt |
| **Display MOSI** | GPIO23 | SPI-Daten |
| **Display CS** | GPIO17 | Chip Select |
| **Encoder A** | GPIO32 | Drehgeber Kanal A |
| **Encoder B** | GPIO33 | Drehgeber Kanal B |
| **Encoder BTN** | GPIO19 | Drucktaste (aktiv-Low) |
| **Summer** | GPIO16 | PWM-Ausgang |
| **Wägezelle DT** | GPIO21 | HX711 Daten (`waegezelle`-Paket) |
| **Wägezelle SCK** | GPIO22 | HX711 Takt (`waegezelle`-Paket) |
| **I2C SDA** | GPIO13 | Für optionale Erweiterungen |
| **I2C SCL** | GPIO14 | Für optionale Erweiterungen |
| **Schrittmotor STEP** | GPIO25 | Wickler-Schrittimpuls |
| **Schrittmotor DIR** | GPIO26 | Wickler-Richtung |
| **Filament-Sensor** | GPIO27 | Runout-Erkennung (aktiv-Low) |
| **Geschwindigkeit (Poti)** | GPIO34 | Analogeingang (0–3,3 V), nur Eingang |
| **Add-on-Erkennung** | GPIO05 | Eingang mit internem Pull-up |

> ⚠️ GPIO34–GPIO39 am ESP32 sind **nur als Eingang** nutzbar und unterstützen keine internen Pull-up/Pull-down-Widerstände. Bei Bedarf externen 10-kΩ-Pull-up verwenden.

---

## Verdrahtungspläne

### Wägezelle → HX711 → ESP32

```
Wägezelle         HX711          ESP32
─────────         ─────          ─────
ROT     (E+)   ──►  E+
SCHWARZ (E-)   ──►  E-
WEISS   (A-)   ──►  A-
GRÜN    (A+)   ──►  A+
                    VCC ──────────►  3,3 V
                    GND ──────────►  GND
                    DT  ──────────►  GPIO21
                    SCK ──────────►  GPIO22
```

> ℹ️ Die Kabelfarben der Wägezelle können je nach Hersteller abweichen. Das Datenblatt der jeweiligen Zelle prüfen.

### ST7920 Display → ESP32 (SPI-Modus) (von einem alten Ender3 or Ender5)

```
ST7920            ESP32
──────            ─────
VCC  ──────────►  5 V (oder 3,3 V – Modul-Datenblatt prüfen)
GND  ──────────►  GND
RS   ──────────►  GPIO17 (CS)
RW   ──────────►  GPIO23 (MOSI)
E    ──────────►  GPIO18 (CLK)
PSB  ──────────►  GND    (erzwingt SPI-Modus)
RST  ──────────►  3,3 V  (oder freier GPIO für Software-Reset)
BLA  ──────────►  3,3 V  (Hintergrundbeleuchtung Anode)
BLK  ──────────►  GND    (Hintergrundbeleuchtung Kathode)
```

### Schrittmotor → A4988 → ESP32

```
ESP32             A4988          NEMA17
─────             ─────          ──────
GPIO25 ────────►  STEP
GPIO26 ────────►  DIR
3,3 V  ────────►  VDD (Logik)
GND    ────────►  GND (Logik)
                  VMOT ◄──── 12-V-Netzteil (+)
                  GND  ◄──── 12-V-Netzteil (-)
                  1A/1B ────► Spule A
                  2A/2B ────► Spule B
```

> ⚠️ Den Strombegrenzungspoti des A4988 vor der Inbetriebnahme einstellen, um Überhitzung des Schrittmotors zu vermeiden.

---

## Stromversorgung

Der ESP32 und die Logikkomponenten laufen mit **3,3 V** (auf dem Board geregelt). Der Schrittmotor benötigt eine separate **12-V-Versorgung**, die direkt am Schrittmotortreiber (VMOT/GND) angeschlossen wird. Den Schrittmotor nicht über die 3,3-V- oder 5-V-Schiene des ESP32 betreiben.

> **Note:** Dieser Bereich wird sich vermutlich noch ändern. Ziel ist eine einheitliche 12V Stromversorgung für die gesamte Hardware.

| Schiene | Verbraucher | Empfohlene Versorgung |
| :--- | :--- | :--- |
| 5 V (via USB) | ESP32, HX711, Display, Encoder, Summer | USB-Netzteil ≥ 1 A |
| 12 V | Schrittmotor (nur Wickler-Add-on) | 12-V-Netzteil / 2 A |

---

## Hinweise zum optionalen Wickler-Add-on

Das Wicklermodul wird über einen dedizierten Stecker verbunden. Seine Anwesenheit wird über **GPIO05** erkannt (intern High gezogen; durch den Add-on-Stecker auf GND gelegt). Ist kein Wickler angesteckt, werden alle Wickler-Menüpunkte automatisch ausgeblendet.

Beim Bau des Wicklermoduls sicherstellen, dass der Stecker GPIO05 im eingesteckten Zustand mit GND verbindet.
