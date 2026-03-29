# 📊 Web Interface

> 🇩🇪 [Deutsche Version weiter unten](#-web-interface-1)

---

The Filament Scale includes a built-in web interface accessible directly from any browser on your local network — no app, no Home Assistant required.

The interface is powered by **ESPHome Web Server v3** and supports both light and dark mode automatically based on your system preferences.

---

## Accessing the Interface

Open a browser and navigate to:

```
http://fila-scale.local
```

Or use the device's IP address directly:

```
http://192.168.x.x
```

> ℹ️ mDNS (`fila-scale.local`) requires a compatible operating system. Windows 10+, macOS, and Linux with Avahi all support this out of the box.

---

## Interface Overview

### Light Mode

![Web Interface — Light Mode](https://christophcaina.github.io/filament_scale/images/webinterface-light.png)
*Web interface in light mode showing Sensor and Control section*

### Dark Mode

![Web Interface — Dark Mode](https://christophcaina.github.io/filament_scale/images/webinterface-dark.png)
*Web interface in dark mode showing Sensor and Control section*

---

## Sensor and Control

![Sensor and Control Section](https://christophcaina.github.io/filament_scale/images/webinterface-sensors.png)

| Entity | Type | Description |
| :--- | :--- | :--- |
| **Filament Remaining** | Sensor | Remaining filament in % |
| **Weight** | Sensor | Current weight in grams |
| **Sync** | Button | Push current weight to Spoolman / FilaMan |
| **Winder Add-on** | Binary Sensor | Winder add-on detected at boot |
| **Winder Speed** | Sensor | Current winder motor speed in % |
| **Winder Toggle** | Button | Toggle winder motor on/off |

---

## Configuration

![Configuration Section](https://christophcaina.github.io/filament_scale/images/webinterface-config.png)

| Entity | Type | Description |
| :--- | :--- | :--- |
| **Calibration Factor** | Number (Slider) | Scale calibration factor — see [Calibration Guide](calibration.md) |
| **Empty Spool Weight** | Number (Slider) | Weight of the empty spool in grams |
| **Total Filament Weight** | Number (Slider) | Total filament weight when full in grams |
| **Spoolman IP** | Text | IP address and port of your Spoolman instance |
| **FilaMan IP** | Text | IP address and port of your FilaMan instance |
| **Tare Scale** | Button | Zero the scale with an empty platform |
| **Firmware Update** | Update | Shows current firmware status and triggers OTA update |

> ℹ️ All configuration values are stored directly on the device and survive reboots. No reflash required.

---

## Diagnostic

| Entity | Type | Description |
| :--- | :--- | :--- |
| **Scale Raw** | Sensor | Raw HX711 value — used for calibration |

---

## OTA Update

The web interface also provides a manual OTA update option at the bottom of the page. You can select a local `.bin` file and flash it directly without Home Assistant or ESPHome Dashboard.

---

## Features

- ✅ No app or Home Assistant required
- ✅ Light and dark mode (follows system preference)
- ✅ Sliders for number entities (Calibration Factor, Spool Weights)
- ✅ Sensor history graphs (click any sensor to expand)
- ✅ Manual OTA update via file upload
- ✅ Live debug log

---
---

# 📊 Web-Interface

Das Filament Scale verfügt über ein eingebautes Web-Interface, das direkt über jeden Browser im lokalen Netzwerk erreichbar ist — ohne App, ohne Home Assistant.

Das Interface basiert auf dem **ESPHome Web Server v3** und unterstützt automatisch Light und Dark Mode basierend auf den Systemeinstellungen.

---

## Zugriff auf das Interface

Browser öffnen und navigieren zu:

```
http://fila-scale.local
```

Oder direkt über die IP-Adresse:

```
http://192.168.x.x
```

> ℹ️ mDNS (`fila-scale.local`) erfordert ein kompatibles Betriebssystem. Windows 10+, macOS und Linux mit Avahi unterstützen dies standardmäßig.

---

## Interface-Übersicht

### Light Mode

![Web Interface — Light Mode](https://christophcaina.github.io/filament_scale/images/webinterface-light.png)
*Web-Interface im Light Mode mit Sensor- und Steuerbereich*

### Dark Mode

![Web Interface — Dark Mode](https://christophcaina.github.io/filament_scale/images/webinterface-dark.png)
*Web-Interface im Dark Mode mit Sensor- und Steuerbereich*

---

## Sensoren und Steuerung

![Sensor and Control Section](https://christophcaina.github.io/filament_scale/images/webinterface-sensors.png)

| Entität | Typ | Beschreibung |
| :--- | :--- | :--- |
| **Filament Remaining** | Sensor | Verbleibendes Filament in % |
| **Weight** | Sensor | Aktuelles Gewicht in Gramm |
| **Sync** | Button | Aktuelles Gewicht an Spoolman / FilaMan senden |
| **Winder Add-on** | Binärsensor | Wickler-Erweiterung beim Boot erkannt |
| **Winder Speed** | Sensor | Aktuelle Wickler-Motorgeschwindigkeit in % |
| **Winder Toggle** | Button | Wicklermotor ein-/ausschalten |

---

## Konfiguration

![Configuration Section](https://christophcaina.github.io/filament_scale/images/webinterface-config.png)

| Entität | Typ | Beschreibung |
| :--- | :--- | :--- |
| **Calibration Factor** | Number (Slider) | Kalibrierungsfaktor der Waage — siehe [Kalibrierungsanleitung](calibration.md) |
| **Empty Spool Weight** | Number (Slider) | Leergewicht der Spule in Gramm |
| **Total Filament Weight** | Number (Slider) | Gesamtfilamentgewicht bei voller Spule in Gramm |
| **Spoolman IP** | Text | IP-Adresse und Port der Spoolman-Instanz |
| **FilaMan IP** | Text | IP-Adresse und Port der FilaMan-Instanz |
| **Tare Scale** | Button | Waage mit leerer Plattform nullen |
| **Firmware Update** | Update | Zeigt Firmware-Status und löst OTA-Update aus |

> ℹ️ Alle Konfigurationswerte werden direkt auf dem Gerät gespeichert und überleben Neustarts. Kein erneutes Flashen erforderlich.

---

## Diagnose

| Entität | Typ | Beschreibung |
| :--- | :--- | :--- |
| **Scale Raw** | Sensor | Rohwert des HX711 — wird für die Kalibrierung verwendet |

---

## OTA Update

Das Web-Interface bietet am unteren Ende der Seite eine manuelle OTA-Update-Option. Eine lokale `.bin`-Datei kann direkt ausgewählt und geflasht werden — ohne Home Assistant oder ESPHome Dashboard.

---

## Features

- ✅ Keine App oder Home Assistant erforderlich
- ✅ Light und Dark Mode (folgt Systemeinstellung)
- ✅ Slider für Number-Entitäten (Kalibrierungsfaktor, Spulengewichte)
- ✅ Sensor-Verlaufsgraphen (Klick auf einen Sensor zum Aufklappen)
- ✅ Manuelles OTA-Update per Datei-Upload
- ✅ Live Debug-Log
