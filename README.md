# ⚖️ ESPHome Filament Spool Scale & Winder

> 🇩🇪 [Deutsche Version weiter unten](#-esphome-filament-waage--wickler)

---

This project is an intelligent filament scale based on an ESP32. It offers precise weight measurement for 3D printing spools, an integrated spool database, and control for an optional winder add-on.  
The Primary goal of this project was also, to re-use some Hardware of an old Ender3 Printer, which is no longer in use.  
If you don't have the required parts remaining, consider to buy some cheap replacement parts online.

## ✨ Features

* **Precise Measurement:** Integration of a load cell (via `hx711` package) for real-time weight monitoring.
* **Smart UI:** Menu-driven display (via `st7920` package) with pagination.
<!-- * **Winder Mode:** Automatic detection of a stepper add-on for filament winding or unloading. -->
<!-- * **Potentiometer Control:** Stepless speed control of the stepper motor via analog potentiometer. -->
* **Modular Design:** Thanks to ESPHome "Packages," the logic is strictly separated from the hardware configuration.
* **Spoolman Integration:** Automatic sync of spool weight and status with your [Spoolman](https://github.com/Donkie/Spoolman) instance.
* **FilaMan Integration:** Compatible with [FilaMan](https://docu.filaman.app/) — a web-based open-source filament management system. Measured spool weights can be pushed to FilaMan for inventory tracking, fill-level monitoring, and printer integration (OctoPrint, Klipper/Moonraker).

---

## 🛠 Hardware & Pinout

Configuration is handled via `substitutions` in the local YAML file. The following pins are assigned by default:

| Component | Pin | Description |
| :--- | :--- | :--- |
| **Status LED** | GPIO02 | System status indicator |
| **Display (SPI)** | GPIO18 (CLK), GPIO23 (MOSI), GPIO17 (CS) | ST7920 128x64 LCD (`st7920` package) |
| **Rotary Encoder** | GPIO32 (A), GPIO33 (B), GPIO19 (BTN) | Navigation & Selection |
| **Buzzer** | GPIO16 | Acoustic feedback |
| **Load Cell (Scale)** | GPIO21 (DT), GPIO22 (SCK) | HX711 interface (`hx711` package) |
| **I2C Interface** | GPIO13 (SDA), GPIO14 (SCL) | Extensions(*) |
<!-- | **Stepper Motor** | GPIO25 (STEP), GPIO26 (DIR) | Winder drive | -->
<!-- | **Filament Sensor** | GPIO27 | Runout detection |-->
<!-- | **Speed** | GPIO34 (Analog) | Potentiometer (0 – 3.3V) | -->
<!-- | **Add-on Detection** | GPIO05 (Inp. Pullup) | Detects plugged-in winder module | -->

> **Note:** It is planned to add some extensions as an optional addon to this scale in the future, like a Spool-Rewinder and addon-detection. These will be added and documented once the hardware-design is finalized
---

## 🚀 Installation & Setup
this section isn't ready yet and will be adjusted over time.

<!-- To use this project on your hardware, follow these steps:

1. Create a **new device** in your ESPHome dashboard.
2. Copy the content of the [example_config.yaml](example_config.yaml) file from this repository.
3. Replace the content of your local configuration in the ESPHome dashboard with the copied text.
4. **Customization:**
   * Enter your WiFi credentials (`ssid` & `password`).
   * API and OTA keys are usually generated automatically by ESPHome when creating the device – ensure these are kept or regenerated.
   * Check the `substitutions` section to ensure the pinout matches your hardware.
5. **Compile & Install:** Click "Install" (USB or OTA).
6. **First Start:** After booting, zero the scale once via the menu item `1. Tare`.

### Spoolman Configuration

To configure Spoolman, set the substitution in the `example_config.yaml` to your Spoolman instance:

```yaml
spoolman_ip: "192.168.0.1:7912"
```

### FilaMan Configuration

[FilaMan](https://docu.filaman.app/) is a web-based open-source application for managing your filament inventory. It supports fill-level monitoring, manufacturer & filament tracking, storage locations, and printer integrations (OctoPrint, Klipper/Moonraker).

To connect this scale to FilaMan, set the corresponding substitution in the `example_config.yaml`:

```yaml
filaman_ip: "192.168.0.1:8083"
```

FilaMan can also be run as a **Home Assistant Add-on** — see [ha-filaman-system](https://github.com/netscout2001/ha-filaman-system).
-->

## Documentation:
* [Concept](docs/concept.md)
* [Hardware](docs/hardware.md)
* [Calibration](docs/calibration.md)
* [API Documentation](docs/api.md)

---

 
## 📄 License / Lizenz
 
This project uses different licenses for different components:
 
| Component | License |
| :--- | :--- |
| **Firmware & Software** (YAML, Scripts, Workflows) | [MIT License](LICENSE) |
| **Hardware** (3D Print Files, PCB Layouts, Schematics) | [CC BY-NC-SA 4.0](LICENSE-HARDWARE) |
| **Documentation** | [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |
 
### MIT License (Software)
The firmware and all software components are free to use, modify and distribute — including for commercial purposes — as long as the copyright notice is retained.
 
### CC BY-NC-SA 4.0 (Hardware)
Hardware design files (3D prints, PCB layouts) may be freely used, shared and adapted for **non-commercial purposes**, as long as appropriate credit is given and derivatives are shared under the same license.
 
> ℹ️ Affiliate links in the documentation do not constitute commercial use of the hardware designs.
 
---

# ⚖️ ESPHome Filament-Waage & -Wickler

Dieses Projekt ist eine intelligente Filamentwaage auf Basis eines ESP32. Es bietet präzise Gewichtsmessung für 3D-Druck-Spulen, eine integrierte Spulendatenbank und die Steuerung eines optionalen Wickler-Add-ons. 
Ein Ziel für dieses Projekt war zudem die Verwertung einiger Bauteile eines alten, ausgedienten Ender3 Druckers.  
Solltest du nicht über die Notwendige Hardware verfügen, kannst du diese relativ günstig Online als Ersatzteile beziehen.

## ✨ Features

* **Präzise Messung:** Integration einer Wägezelle (über das `hx711`-Paket) zur Echtzeit-Gewichtsüberwachung.
* **Smartes UI:** Menügeführtes Display (über das `st7920`-Paket) mit Paginierung.
<!-- * **Wickler-Modus:** Automatische Erkennung eines Stepper-Add-ons zum Aufwickeln oder Entladen von Filament. -->
<!-- * **Poti-Steuerung:** Stufenlose Geschwindigkeitsregelung des Schrittmotors über ein analoges Potentiometer. -->
* **Modulares Design:** Dank ESPHome-„Packages" ist die Logik strikt von der Hardware-Konfiguration getrennt.
* **Spoolman-Anbindung:** Automatische Synchronisierung von Spulengewicht und -status mit deiner [Spoolman](https://github.com/Donkie/Spoolman)-Instanz.
* **FilaMan-Anbindung:** Kompatibel mit [FilaMan](https://docu.filaman.app/) – einem webbasierten Open-Source-System zur Filamentverwaltung. Gemessene Spulengewichte können an FilaMan übertragen werden: für Bestandsverwaltung, Füllstandsüberwachung und Druckerintegration (OctoPrint, Klipper/Moonraker).

---

## 🛠 Hardware & Pinbelegung

Die Konfiguration erfolgt über `substitutions` in der lokalen YAML-Datei. Folgende Pins sind standardmäßig belegt:

| Komponente | Pin | Beschreibung |
| :--- | :--- | :--- |
| **Status-LED** | GPIO02 | Systemstatusanzeige |
| **Display (SPI)** | GPIO18 (CLK), GPIO23 (MOSI), GPIO17 (CS) | ST7920 128x64 LCD (`st7920`-Paket) |
| **Drehgeber** | GPIO32 (A), GPIO33 (B), GPIO19 (BTN) | Navigation & Auswahl |
| **Summer** | GPIO16 | Akustisches Feedback |
| **Wägezelle** | GPIO21 (DT), GPIO22 (SCK) | HX711-Schnittstelle (`hx711`-Paket) |
| **I2C-Interface** | GPIO13 (SDA), GPIO14 (SCL) | Erweiterungen(*) |
<!-- | **Schrittmotor** | GPIO25 (STEP), GPIO26 (DIR) | Wicklerantrieb | -->
<!-- | **Filament-Sensor** | GPIO27 | Erkennung von Filamentmangel | -->
<!-- | **Geschwindigkeit** | GPIO34 (Analog) | Potentiometer (0 – 3,3 V) |-->
<!--| **Add-on-Erkennung** | GPIO05 (Inp. Pullup) | Erkennt angestecktes Wickler-Modul |-->

> **Notiz:** Es ist zu einem späteren Zeitpunkt geplant, Erweiterungen wie einen Spulen-Wickler und eine Addonerkennung als Optionale Features einzubinden. Diese werden hier dokumentiert, sobald die Hardware finalisiert wurde.
---

## 🚀 Installation & Einrichtung
Dieser Abschnitt ist aktuell noch nicht fertig gestellt und wird über die Zeit hin angepasst.
<!-- So verwendest du dieses Projekt auf deiner Hardware:

1. Erstelle ein **neues Gerät** in deinem ESPHome-Dashboard.
2. Kopiere den Inhalt der Datei [example_config.yaml](example_config.yaml) aus diesem Repository.
3. Ersetze den Inhalt deiner lokalen Konfiguration im ESPHome-Dashboard durch den kopierten Text.
4. **Anpassung:**
   * Trage deine WLAN-Zugangsdaten ein (`ssid` & `password`).
   * API- und OTA-Schlüssel werden von ESPHome beim Anlegen des Geräts normalerweise automatisch generiert – stelle sicher, dass diese übernommen oder neu generiert werden.
   * Überprüfe den Abschnitt `substitutions`, um sicherzustellen, dass die Pinbelegung deiner Hardware entspricht.
5. **Kompilieren & Installieren:** Klicke auf „Install" (USB oder OTA).
6. **Erster Start:** Starte nach dem Booten einmalig die Tarierung über den Menüpunkt `1. Tare`.

### Spoolman-Konfiguration

Um Spoolman zu konfigurieren, trage in der `example_config.yaml` die IP deiner Spoolman-Instanz ein:

```yaml
spoolman_ip: "192.168.0.1:7912"
```

### FilaMan-Konfiguration

[FilaMan](https://docu.filaman.app/) ist eine webbasierte Open-Source-Anwendung zur Verwaltung deines Filamentbestands. Sie unterstützt Füllstandsüberwachung, Hersteller- & Filamentverwaltung, Lagerorte sowie Druckerintegration (OctoPrint, Klipper/Moonraker).

Um diese Waage mit FilaMan zu verbinden, trage die entsprechende Substitution in der `example_config.yaml` ein:

```yaml
filaman_ip: "192.168.0.1:8083"
```

FilaMan kann auch als **Home Assistant Add-on** betrieben werden – siehe [ha-filaman-system](https://github.com/netscout2001/ha-filaman-system).
-->

## Dokumentation:
* [Konzept](docs/concept.md#-konzept--architektur)
* [Hardware](docs/hardware.md#-hardware-dokumentation)
* [Kalibrierung](docs/calibration.md#voraussetzungen)
* [API Dokumentation](docs/api.md#-api-dokumentation)

---
 
Dieses Projekt verwendet verschiedene Lizenzen für verschiedene Komponenten:
 
| Komponente | Lizenz |
| :--- | :--- |
| **Firmware & Software** (YAML, Scripts, Workflows) | [MIT Lizenz](LICENSE) |
| **Hardware** (3D-Druck-Dateien, PCB-Layouts, Schaltpläne) | [CC BY-NC-SA 4.0](LICENSE-HARDWARE) |
| **Dokumentation** | [CC BY-SA 4.0](https://creativecommons.org/licenses/by-sa/4.0/) |
 
### MIT Lizenz (Software)
Firmware und alle Software-Komponenten dürfen frei verwendet, verändert und weitergegeben werden — auch kommerziell — solange der Copyright-Hinweis erhalten bleibt.
 
### CC BY-NC-SA 4.0 (Hardware)
Hardware-Designdateien (3D-Drucke, PCB-Layouts) dürfen für **nicht-kommerzielle Zwecke** frei genutzt, geteilt und angepasst werden, sofern eine angemessene Namensnennung erfolgt und Ableitungen unter der gleichen Lizenz veröffentlicht werden.
 
> ℹ️ Affiliate-Links in der Dokumentation stellen keine kommerzielle Nutzung der Hardware-Designs dar.
 