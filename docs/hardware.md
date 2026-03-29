# 🛠 Hardware Documentation

> 🇩🇪 [Deutsche Version weiter unten](#-hardware-dokumentation)

---

This document covers the hardware components used in this project, their wiring, and tips for sourcing and assembly.

---

## Bill of Materials (BOM)

| Component | Description | Qty | Price (approx.) | Link |
| :--- | :--- | :---: | --: | :-- |
| ESP32 Dev Board Mini | e.g. ESP32-WROOM-32 (38-pin) | 1 | - | - |
| **HX711 + Load Cell** | 24-bit ADC for load cells | 1 | 8,98 € | [Amazon ↗](https://amzn.to/4sH5u3X) |
| **ST7920 LCD** | Ender 3 display, 128×64, SPI | 1 | 17,99 € | [Amazon ↗](https://amzn.to/4bV3Lkr) |
| **PN532 NFC Module** | incl. NFC card + keyring chip + Dupont set | 1 | 6,50 € * | [Amazon ↗](https://amzn.to/4s40mFX) |
| PN5180 NFC Modul (v2) | NFC Reader / writer | 1 | - | - |
| **TMC2209 v2** | Stepper driver incl. heat sink, pre-soldered | 1 | 5,52 € * | [Amazon ↗](https://amzn.to/4sF8td9) |
<| **NEMA17 Stepper** | 42 Ncm, for optional winder | 1 | 10,50 € | [Amazon ↗](https://amzn.to/48hPeOv) |
| Power Supply | 12 V | 1 | - | - |
<!-- | Potentiometer | 10 kΩ linear, for winder speed | 1 | -->
<!-- | Filament Runout Sensor*² | Mechanical switch or optical | 1 | -->

*\* Price per unit when buying the multi-pack*
 
**Estimated total cost (buying everything):**
| Configuration | Cost |
| :--- | ---: |
| Base (no stepper) | ~**38,94 €** |
| Full (with stepper) | ~**49,44 €** |
 
> ℹ️ The PN532 pack contains 2 modules — one extra for future use or a second scale. The TMC2209 pack contains 5 — extras are useful for other 3D printer projects.


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

### Stepper Motor → Driver → ESP32

Both **A4988** and **TMC2209** (recommended) are supported. The STEP/DIR interface is identical for both — no firmware changes needed.

| Driver | Pros | Cons |
| :--- | :--- | :--- |
| **TMC2209** *(recommended)* | Very quiet (StealthChop), thermal protection, overcurrent protection | ~3–5€ |
| **A4988** | Cheap, widely available | Loud, minimal protection |

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

 ⚠️ Always set the current limit on the driver before connecting the stepper motor. For TMC2209, adjust the Vref potentiometer. For A4988, use a multimeter to set Vref according to your motor's rated current.
---

## Power Supply

This project uses a **single 12 V input** for all components. A step-down module converts 12 V to 5 V for the ESP32, display, and sensors. The ESP32 regulates 5 V down to 3.3 V internally.

> ⚠️ The Ender 3 display cannot be powered from the ESP32's USB port — it requires a dedicated 5 V supply with sufficient current. Always use the 12 V input with a step-down module.

```
12V PSU (Barrel Jack 5.5/2.1mm)
    ├── Step-Down (12V → 5V) ──► ESP32 VIN
    │                        ──► ST7920 Display VCC
    │                        ──► HX711 VCC
    │                        ──► PN532 NFC VCC
    │                        ──► Rotary Encoder VCC
    │                        ──► Buzzer
    │   (ESP32 regulates 5V → 3.3V internally)
    └── Direct 12V ──► Stepper Driver VMOT (Winder Add-on only)
```

| Rail | Consumers | Source |
| :--- | :--- | :--- |
| **12 V** | Stepper motor (optional) | 12 V PSU via barrel jack |
| **5 V** | ESP32 (VIN), Display, HX711, PN532, Encoder, Buzzer | Step-down module (e.g. MP1584) |
| **3.3 V** | ESP32 internals, sensors | ESP32 on-board regulator |

### Recommended Step-Down Module

Any adjustable step-down module works. Common options:

| Module | Input | Output | Notes |
| :--- | :--- | :--- | :--- |
| **MP1584** | 4.5–28 V | 0.8–20 V adj. | Compact, ~2€, 3A |
| **LM2596** | 4–40 V | 1.5–37 V adj. | Widely available, ~1€ |
| **XL4016** | 4–40 V | 1.25–36 V adj. | Higher current, ~2€ |

Set the output to **5.0 V** before connecting any components.
---

## Notes on the Optional Winder Add-on

The winder module connects via a dedicated connector. Its presence is detected via **GPIO05** (pulled high internally; grounded by the add-on connector). When not connected, all winder-related menu items are hidden automatically.

If you are building the winder module, ensure the connector shorts GPIO05 to GND when plugged in.

---

## Connectors & Assembly Options

This project is designed to be built in stages — from a quick breadboard prototype to a finished PCB. The connector choices reflect standard maker inventory.

### Prototyping (Breadboard)

For initial testing, use **Dupont jumper wires** (2.54mm) directly on the breadboard. All components connect via standard 2.54mm headers which are breadboard-compatible.

### Connectors for Permanent Assembly

All connectors are chosen to be compatible with **standard 2.54mm pitch perfboard/stripboard** (the most common prototype PCB kit format).

| Connection | Connector Type | Pitch | Pins | Notes |
| :--- | :--- | :--- | :---: | :--- |
| **Load Cell → HX711** | JST XH | 2.54mm | 4 | Red, Black, White, Green |
| **HX711 → ESP32** | Dupont / Pin Header | 2.54mm | 4 | DT, SCK, VCC, GND |
| **Display (ST7920)** | Dupont / Pin Header | 2.54mm | 10 | EXP1/EXP2 compatible |
| **Rotary Encoder** | Dupont / Pin Header | 2.54mm | 5 | CLK, DT, SW, VCC, GND |
| **Buzzer** | Dupont / Pin Header | 2.54mm | 2 | + / GND |
| **NFC (PN532)** | Dupont / Pin Header | 2.54mm | 4 | SDA, SCL, VCC, GND |
| **Stepper Driver** | Dupont / Pin Header | 2.54mm | 4 | STEP, DIR, VDD, GND |
| **12V Power Input** | Barrel Jack (THT) | — | — | 5.5mm/2.1mm, standard PSU plug |
| **Motor Power** | Screw Terminal | 5.08mm | 2 | VMOT / GND to stepper driver |

> ℹ️ **JST XH (2.54mm)** is the recommended connector for load cell and sensor connections — it fits standard perfboard and prevents accidental disconnection. Most maker supply stores carry JST XH pre-crimped cables.

> ℹ️ **Barrel Jack (5.5/2.1mm THT)** for 12V input is universally available and compatible with standard power supplies used for 3D printers.

### Ender 3 Display Connector

The Ender 3 / Ender 5 display uses two **10-pin Dupont connectors (2×5, 2.54mm pitch)** labeled EXP1 and EXP2. Standard Dupont housings and pins are used — no special cable required.

```
EXP1 / EXP2 Pinout (2×5, 2.54mm)
┌─────────────────────┐
│ 1  2  3  4  5       │
│ 6  7  8  9  10      │
└─────────────────────┘
Pin 1 = top-left (marked with triangle on PCB)
```

### Perfboard Compatibility

All connectors use **2.54mm pitch** to match the standard hole spacing of prototype PCB kits (e.g. 2×8cm, 3×7cm, 5×7cm boards). No special tools required beyond a soldering iron.

---
---

# 🛠 Hardware-Dokumentation

Dieses Dokument beschreibt die verwendeten Hardware-Komponenten, ihre Verdrahtung sowie Tipps zur Beschaffung und zum Aufbau.

---

## Stückliste (BOM)

| Komponente | Beschreibung | Menge | Preis (ca.) | Link |
| :--- | :--- | :---: | --: | :-- |
| ESP32 Dev Board Mini | e.g. ESP32-WROOM-32 (38-pin) | 1 | - | - |
| **HX711 + 5 kg Wägezelle** | 24-bit ADC for Wägezellen | 1 | 8,98 € | [Amazon ↗](https://amzn.to/4sH5u3X) |
| **ST7920 LCD** | Ender 3 display, 128×64, SPI | 1 | 17,99 € | [Amazon ↗](https://amzn.to/4bV3Lkr) |
| **PN532 NFC Modul** (v1) | inkl. NFC Karte + Schlüsselanhänger Chip + Dupont set | 1 | 6,50 € * | [Amazon ↗](https://amzn.to/4s40mFX) |
| PN5180 NFC Modul (v2) | NFC Leser / schreiber | 1 | - | - |
| **TMC2209 v2** | Stepper Treiber inkl. heat sink, Vorgelötet | 1 | 5,52 € * | [Amazon ↗](https://amzn.to/4sF8td9) |
<| **NEMA17 Stepper Motor** | 42 Ncm, für Optionalen Winder | 1 | 10,50 € | [Amazon ↗](https://amzn.to/48hPeOv) |
| Netzteil | 12 V | 1 | - | - |
<!-- | Potentiometer | 10 kΩ linear, for winder speed | 1 | -->
<!-- | Filament Runout Sensor*² | Mechanical switch or optical | 1 | -->

*\* Price per unit when buying the multi-pack*
 
**Estimated total cost (buying everything):**
| Configuration | Cost |
| :--- | ---: |
| Base (no stepper) | ~**38,94 €** |
| Full (with stepper) | ~**49,44 €** |
 
> ℹ️ The PN532 pack contains 2 modules — one extra for future use or a second scale. The TMC2209 pack contains 5 — extras are useful for other 3D printer projects.

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

### Schrittmotor → Treiber → ESP32

Sowohl **A4988** als auch **TMC2209** (empfohlen) werden unterstützt. Das STEP/DIR-Interface ist bei beiden identisch — keine Firmware-Änderungen nötig.

| Treiber | Vorteile | Nachteile |
| :--- | :--- | :--- |
| **TMC2209** *(empfohlen)* | Sehr leise (StealthChop), Überhitzungsschutz, Überstromschutz | ~3–5€ |
| **A4988** | Günstig, überall verfügbar | Laut, minimaler Schutz |

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

> ⚠️ Den Strombegrenzungspoti vor dem Anschluss des Schrittmotors einstellen. Beim TMC2209 den Vref-Poti entsprechend dem Nennstrom des Motors einstellen. Beim A4988 den Vref-Wert per Multimeter messen.

---

## Stromversorgung

Dieses Projekt verwendet einen **einzigen 12-V-Eingang** für alle Komponenten. Ein Stepdown-Modul wandelt 12 V auf 5 V für ESP32, Display und Sensoren um. Der ESP32 regelt 5 V intern auf 3,3 V herunter.

> ⚠️ Das Ender-3-Display kann **nicht** über den USB-Port des ESP32 betrieben werden — es benötigt eine dedizierte 5-V-Versorgung mit ausreichend Strom. Immer den 12-V-Eingang mit Stepdown-Modul verwenden.

```
12V Netzteil (Hohlstecker 5,5/2,1mm)
    ├── Stepdown (12V → 5V) ──► ESP32 VIN
    │                       ──► ST7920 Display VCC
    │                       ──► HX711 VCC
    │                       ──► PN532 NFC VCC
    │                       ──► Drehgeber VCC
    │                       ──► Summer
    │   (ESP32 regelt intern 5V → 3,3V)
    └── Direkt 12V ──► Schrittmotortreiber VMOT (nur Wickler-Add-on)
```

| Schiene | Verbraucher | Quelle |
| :--- | :--- | :--- |
| **12 V** | Schrittmotor (optional) | 12-V-Netzteil via Hohlstecker |
| **5 V** | ESP32 (VIN), Display, HX711, PN532, Encoder, Summer | Stepdown-Modul (z.B. MP1584) |
| **3,3 V** | ESP32-Interna, Sensoren | ESP32 On-Board-Regler |

### Empfohlene Stepdown-Module

Jedes einstellbare Stepdown-Modul ist geeignet. Gängige Optionen:

| Modul | Eingang | Ausgang | Hinweise |
| :--- | :--- | :--- | :--- |
| **MP1584** | 4,5–28 V | 0,8–20 V einstellbar | Kompakt, ~2€, 3A |
| **LM2596** | 4–40 V | 1,5–37 V einstellbar | Weit verbreitet, ~1€ |
| **XL4016** | 4–40 V | 1,25–36 V einstellbar | Höherer Strom, ~2€ |

Ausgang auf **5,0 V** einstellen **bevor** Komponenten angeschlossen werden.
---

## Hinweise zum optionalen Wickler-Add-on

Das Wicklermodul wird über einen dedizierten Stecker verbunden. Seine Anwesenheit wird über **GPIO05** erkannt (intern High gezogen; durch den Add-on-Stecker auf GND gelegt). Ist kein Wickler angesteckt, werden alle Wickler-Menüpunkte automatisch ausgeblendet.

Beim Bau des Wicklermoduls sicherstellen, dass der Stecker GPIO05 im eingesteckten Zustand mit GND verbindet.

---
 
## Steckverbinder & Aufbauoptionen
 
Dieses Projekt ist so konzipiert, dass es stufenweise aufgebaut werden kann — vom schnellen Breadboard-Prototypen bis zur fertigen Platine. Die Steckverbinder entsprechen dem Standard-Maker-Inventar.
 
### Prototyping (Breadboard)
 
Für erste Tests können **Dupont Jumper-Kabel (2.54mm)** direkt auf dem Breadboard verwendet werden. Alle Komponenten nutzen Standard-2.54mm-Stiftleisten, die breadboard-kompatibel sind.
 
### Steckverbinder für den permanenten Aufbau
 
Alle Steckverbinder sind kompatibel mit **Standard-2.54mm-Lochrasterplatinen** (das gängigste Format in Prototypen-PCB-Kits).
 
| Verbindung | Steckverbinder | Raster | Pins | Hinweise |
| :--- | :--- | :--- | :---: | :--- |
| **Wägezelle → HX711** | JST XH | 2.54mm | 4 | Rot, Schwarz, Weiß, Grün |
| **HX711 → ESP32** | Dupont / Stiftleiste | 2.54mm | 4 | DT, SCK, VCC, GND |
| **Display (ST7920)** | Dupont / Stiftleiste | 2.54mm | 10 | EXP1/EXP2 kompatibel |
| **Drehgeber (Encoder)** | Dupont / Stiftleiste | 2.54mm | 5 | CLK, DT, SW, VCC, GND |
| **Summer (Buzzer)** | Dupont / Stiftleiste | 2.54mm | 2 | + / GND |
| **NFC (PN532)** | Dupont / Stiftleiste | 2.54mm | 4 | SDA, SCL, VCC, GND |
| **Schrittmotor-Treiber** | Dupont / Stiftleiste | 2.54mm | 4 | STEP, DIR, VDD, GND |
| **12V Eingang** | Hohlstecker-Buchse (THT) | — | — | 5.5mm/2.1mm, Standard-Netzteilstecker |
| **Motorstrom** | Schraubklemme | 5.08mm | 2 | VMOT / GND zum Schrittmotortreiber |
 
> ℹ️ **JST XH (2.54mm)** wird für Wägezellen- und Sensoranschlüsse empfohlen — passt auf Lochrasterplatinen und verhindert versehentliches Trennen. In den meisten Maker-Shops gibt es JST XH als vorkonfektionierte Kabel.
 
> ℹ️ **Hohlstecker-Buchse (5.5/2.1mm THT)** für den 12V-Eingang ist universell verfügbar und kompatibel mit Standard-Netzteilen für 3D-Drucker.
 
### Ender 3 Display-Stecker
 
Das Ender 3 / Ender 5 Display verwendet zwei **10-polige Dupont-Stecker (2×5, 2.54mm Raster)**, beschriftet als EXP1 und EXP2. Standard-Dupont-Gehäuse und Pins werden verwendet — kein Spezialkabel erforderlich.
 
```
EXP1 / EXP2 Belegung (2×5, 2.54mm)
┌─────────────────────┐
│ 1  2  3  4  5       │
│ 6  7  8  9  10      │
└─────────────────────┘
Pin 1 = oben links (auf der Platine mit Dreieck markiert)
```
 
### Lochraster-Kompatibilität
 
Alle Steckverbinder verwenden **2.54mm Raster** passend zum Standard-Lochabstand der gängigen Prototypen-PCB-Kits (z.B. 2×8cm, 3×7cm, 5×7cm Platinen). Kein Spezialwerkzeug erforderlich — nur ein Lötkolben.