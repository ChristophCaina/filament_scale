# 🧠 Concept & Architecture

> 🇩🇪 [Deutsche Version weiter unten](#-konzept--architektur)

---

## Overview

This project is built around ESPHome's **Package system**, which allows splitting a firmware project into multiple reusable YAML modules. The goal is a clean separation between hardware configuration (pins, IDs) and application logic (menus, automations, integrations).

---

## Architecture / Package Structure

```
example_config.yaml          ← Local device file (user-owned)
│
├── substitutions:           ← All hardware-specific values (pins, IPs, keys)
│
└── packages:
    ├── waegezelle.yaml      ← Load cell (HX711): filters, calibration, tare
    ├── display.yaml         ← Display (ST7920): menu rendering, pagination
    ├── encoder.yaml         ← Rotary encoder: navigation input
    ├── winder.yaml          ← Stepper motor: winder control, speed, addon detection
    ├── spoolman.yaml        ← Spoolman HTTP integration
    ├── filaman.yaml         ← FilaMan HTTP integration
    └── base.yaml            ← WiFi, API, OTA, status LED, buzzer
```

The local `example_config.yaml` only contains `substitutions` and `packages` references. **No logic lives in the local file** — this makes updates easy: pull the latest package files without touching your local config.

---

## Design Decisions

### Why ESPHome Packages?

ESPHome packages allow a clean **separation of concerns**:

- The user only edits one file (`example_config.yaml`) with their specific pins and credentials.
- All logic (display menus, sensor filters, HTTP calls) lives in versioned package files.
- Updates to the project can be pulled without overwriting user configuration.

### Why ST7920 + Rotary Encoder instead of a touchscreen?

The ST7920 128×64 LCD was chosen for its simplicity, low power draw, and wide availability. Combined with a rotary encoder it provides reliable tactile navigation without the complexity of touch calibration or capacitive interference near metal parts.

### Why HX711?

The HX711 is the de facto standard for DIY load cell interfaces. It offers 24-bit resolution, built-in amplification, and excellent ESPHome support. The `waegezelle` package handles filtering, tare offsets, and calibration factor application.

### Winder as Optional Add-on

The winder module is intentionally **optional and auto-detected** via a dedicated GPIO input (pulled high, grounded when the add-on is connected). This keeps the base device useful as a standalone scale without the winder attached.

---

## Flowchart

```
Boot
 │
 ├─► Connect WiFi
 │    └─► Register with Home Assistant (if available)
 │
 ├─► Initialize display (display.yaml)
 │    └─► Show boot screen
 │
 ├─► Initialize load cell (waegezelle.yaml)
 │    └─► Apply calibration factor
 │
 ├─► Check Add-on GPIO
 │    ├─► Winder detected → enable winder menu items
 │    └─► No winder → hide winder menu items
 │
 └─► Main Loop
      │
      ├─► Read weight (waegezelle.yaml)
      │    └─► Stable reading?
      │         ├─► Yes → update display, push to Spoolman/FilaMan
      │         └─► No  → continue averaging
      │
      ├─► Encoder input (encoder.yaml)
      │    └─► Navigate menu / select item
      │
      └─► Winder active? (winder.yaml)
           └─► Read potentiometer → set stepper speed
```

---

## Planned Features (Roadmap)

> Features marked with 🔲 are planned but not yet implemented.
> Features marked with ✅ are implemented and tested

| Status | Feature |
| :--- | :--- |
| 🔲 | Precise weight measurement with HX711 |
| 🔲 | ST7920 display with menu navigation |
| 🔲 | Rotary encoder input |
| 🔲 | Spoolman integration (push weight) |
| 🔲 | Optional winder add-on with auto-detection |
| 🔲 | Potentiometer speed control |
| 🔲 | Home Assistant integration |
| 🔲 | FilaMan integration |
| 🔲 | NFC/RFID spool tag reading |
| 🔲 | BambuLab AMS integration |
| 🔲 | Multi-spool profiles stored on device |
| 🔲 | Tare memory per spool (empty spool weight database) |
| 🔲 | OTA update notification via display |

---
---

# 🧠 Konzept & Architektur

---

## Überblick

Dieses Projekt basiert auf dem **Package-System von ESPHome**, das es ermöglicht, ein Firmware-Projekt in mehrere wiederverwendbare YAML-Module aufzuteilen. Ziel ist eine saubere Trennung zwischen Hardware-Konfiguration (Pins, IDs) und Anwendungslogik (Menüs, Automationen, Integrationen).

---

## Architektur / Package-Struktur

```
example_config.yaml          ← Lokale Gerätedatei (nutzereigen)
│
├── substitutions:           ← Alle hardwarespezifischen Werte (Pins, IPs, Keys)
│
└── packages:
    ├── waegezelle.yaml      ← Wägezelle (HX711): Filter, Kalibrierung, Tare
    ├── display.yaml         ← Display (ST7920): Menü-Rendering, Paginierung
    ├── encoder.yaml         ← Drehgeber: Navigationseingabe
    ├── winder.yaml          ← Schrittmotor: Wicklersteuerung, Geschwindigkeit, Add-on-Erkennung
    ├── spoolman.yaml        ← Spoolman HTTP-Integration
    ├── filaman.yaml         ← FilaMan HTTP-Integration
    └── base.yaml            ← WLAN, API, OTA, Status-LED, Summer
```

Die lokale `example_config.yaml` enthält nur `substitutions` und `packages`-Referenzen. **Keine Logik lebt in der lokalen Datei** – Updates können eingespielt werden, ohne die eigene Konfiguration zu überschreiben.

---

## Designentscheidungen

### Warum ESPHome Packages?

ESPHome-Packages ermöglichen eine saubere **Trennung der Verantwortlichkeiten**:

- Der Nutzer bearbeitet nur eine Datei (`example_config.yaml`) mit seinen spezifischen Pins und Zugangsdaten.
- Die gesamte Logik (Display-Menüs, Sensorfilter, HTTP-Aufrufe) lebt in versionierten Package-Dateien.
- Updates können eingespielt werden, ohne die Nutzerkonfiguration zu überschreiben.

### Warum ST7920 + Drehgeber statt Touchscreen?

Das ST7920 128×64 LCD wurde wegen seiner Einfachheit, des geringen Stromverbrauchs und der weiten Verfügbarkeit gewählt. Kombiniert mit einem Drehgeber bietet es zuverlässige haptische Navigation ohne die Komplexität von Touch-Kalibrierung oder kapazitiven Störungen in der Nähe von Metallteilen.

### Warum HX711?

Der HX711 ist der De-facto-Standard für DIY-Wägezelleninterfaces. Er bietet 24-Bit-Auflösung, eingebaute Verstärkung und exzellente ESPHome-Unterstützung. Das `waegezelle`-Paket übernimmt Filterung, Tare-Offsets und die Anwendung des Kalibrierungsfaktors.

### Wickler als optionales Add-on

Das Wickler-Modul ist bewusst **optional und wird automatisch erkannt** – über einen dedizierten GPIO-Eingang (High-gezogen, wird bei angestecktem Add-on auf GND gezogen). So bleibt das Basisgerät als eigenständige Waage verwendbar.

---

## Ablaufdiagramm

```
Start
 │
 ├─► WLAN verbinden
 │    └─► Bei Home Assistant registrieren (falls verfügbar)
 │
 ├─► Display initialisieren (display.yaml)
 │    └─► Bootscreen anzeigen
 │
 ├─► Wägezelle initialisieren (waegezelle.yaml)
 │    └─► Kalibrierungsfaktor anwenden
 │
 ├─► Add-on-GPIO prüfen
 │    ├─► Wickler erkannt → Wickler-Menüpunkte aktivieren
 │    └─► Kein Wickler → Wickler-Menüpunkte ausblenden
 │
 └─► Hauptschleife
      │
      ├─► Gewicht lesen (waegezelle.yaml)
      │    └─► Stabiler Messwert?
      │         ├─► Ja → Display aktualisieren, an Spoolman/FilaMan senden
      │         └─► Nein → Mittelwertbildung fortsetzen
      │
      ├─► Drehgeber-Eingabe (encoder.yaml)
      │    └─► Menü navigieren / Element auswählen
      │
      └─► Wickler aktiv? (winder.yaml)
           └─► Potentiometer lesen → Schrittmotorgeschwindigkeit setzen
```

---

## Geplante Features (Roadmap)

> Mit 🔲 markierte Features sind geplant, aber noch nicht implementiert.
> Mit ✅ markierte Features sind bereits implementiert und getestet.

| Status | Feature |
| :--- | :--- |
| 🔲 | Präzise Gewichtsmessung mit HX711 |
| 🔲 | ST7920-Display mit Menünavigation |
| 🔲 | Drehgeber-Eingabe |
| 🔲 | Spoolman-Integration (Gewicht senden) |
| 🔲 | Optionales Wickler-Add-on mit automatischer Erkennung |
| 🔲 | Potentiometer-Geschwindigkeitsregelung |
| 🔲 | Home Assistant Integration |
| 🔲 | FilaMan-Integration |
| 🔲 | NFC/RFID-Spulentag-Lesung |
| 🔲 | BambuLab AMS-Integration |
| 🔲 | Mehrere Spulenprofile auf dem Gerät gespeichert |
| 🔲 | Tare-Speicher pro Spule (Leerspulen-Gewichtsdatenbank) |
| 🔲 | OTA-Update-Benachrichtigung im Display |