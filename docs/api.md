# 🔌 API Documentation

> 🇩🇪 [Deutsche Version weiter unten](#-api-dokumentation)

---

This document describes all external interfaces of the ESPHome Filament Scale: the native ESPHome REST API, Home Assistant integration, and connections to Spoolman and FilaMan.

---

## ESPHome REST API

ESPHome exposes a built-in REST API that allows reading sensor values and triggering actions without Home Assistant.

**Base URL:** `http://<device-ip>/`

### Sensor States

```http
GET /sensor/<sensor_id>
```

Returns the current state of a sensor as JSON.

| Sensor ID | Description |
| :--- | :--- |
| `filament_weight` | Current net weight in grams |
| `spool_weight` | Raw scale reading |
| `filament_remaining` | Calculated remaining filament in % |

**Example:**
```bash
curl http://192.168.0.50/sensor/filament_weight
```
```json
{ "id": "sensor-filament_weight", "value": 217.4, "state": "217.4 g" }
```

### Switch / Button Actions

```http
POST /button/<button_id>/press
```

| Button ID | Description |
| :--- | :--- |
| `tare_scale` | Zero/tare the scale |
| `sync_spoolman` | Manually trigger Spoolman sync |
| `sync_filaman` | Manually trigger FilaMan sync |

**Example:**
```bash
curl -X POST http://192.168.0.50/button/tare_scale/press
```

---

## Home Assistant Integration

The device integrates natively into Home Assistant via the **ESPHome integration** (API key required).

### Setup

1. In Home Assistant, go to **Settings → Devices & Services → Add Integration → ESPHome**.
2. Enter the device IP address.
3. Enter the API key from your `example_config.yaml`.

### Available Entities

| Entity | Type | Description |
| :--- | :--- | :--- |
| `sensor.filament_weight` | Sensor | Current net weight (g) |
| `sensor.filament_remaining` | Sensor | Remaining filament (%) |
| `button.tare_scale` | Button | Tare the scale |
| `button.sync_spoolman` | Button | Trigger Spoolman sync |
| `switch.winder_enabled` | Switch | Enable/disable winder motor |
| `number.winder_speed` | Number | Winder speed (0–100%) |

### Example Automation

```yaml
automation:
  - alias: "Notify when filament running low"
    trigger:
      - platform: numeric_state
        entity_id: sensor.filament_remaining
        below: 15
    action:
      - service: notify.mobile_app
        data:
          message: "⚠️ Filament below 15% – please reload spool!"
```

---

## Spoolman API

[Spoolman](https://github.com/Donkie/Spoolman) is a self-hosted filament inventory manager. The scale pushes weight updates to Spoolman automatically after each stable measurement.

### Configuration

```yaml
spoolman_ip: "192.168.0.1:7912"
```

### How It Works

After a stable weight reading, the scale sends an HTTP PATCH request to update the remaining weight of the active spool:

```http
PATCH http://<spoolman_ip>/api/v1/spool/<spool_id>
Content-Type: application/json

{ "remaining_weight": 217.4 }
```

The active `spool_id` is selected via the on-device menu or synced from Spoolman on boot.

### Manual Sync

```bash
curl -X POST http://192.168.0.50/button/sync_spoolman/press
```

---

## FilaMan API

[FilaMan](https://docu.filaman.app/) is a web-based open-source filament management system with support for OctoPrint, Klipper/Moonraker, and Home Assistant.

### Configuration

```yaml
filaman_ip: "192.168.0.1:8083"
```

### How It Works

The scale pushes the current spool weight to FilaMan after each stable measurement for fill-level tracking and printer filament accounting.

```http
POST http://<filaman_ip>/api/scale
Content-Type: application/json

{ "weight": 217.4, "spool_id": "abc123" }
```

> ℹ️ The exact endpoint may vary depending on your FilaMan version. Check the [FilaMan documentation](https://docu.filaman.app/) for the current API reference.

### Home Assistant Add-on

FilaMan can be installed as a Home Assistant Add-on via [ha-filaman-system](https://github.com/netscout2001/ha-filaman-system). Use the internal HA IP and the configured port.

---
---

# 🔌 API-Dokumentation

Dieses Dokument beschreibt alle externen Schnittstellen der ESPHome Filamentwaage: die native ESPHome REST API, die Home Assistant Integration sowie die Anbindungen an Spoolman und FilaMan.

---

## ESPHome REST API

ESPHome stellt eine eingebaute REST API bereit, über die Sensorwerte ausgelesen und Aktionen ausgelöst werden können – ohne Home Assistant.

**Basis-URL:** `http://<Geräte-IP>/`

### Sensorwerte abfragen

```http
GET /sensor/<sensor_id>
```

| Sensor-ID | Beschreibung |
| :--- | :--- |
| `filament_weight` | Aktuelles Nettogewicht in Gramm |
| `spool_weight` | Rohwert der Waage |
| `filament_remaining` | Berechnetes Restfilament in % |

**Beispiel:**
```bash
curl http://192.168.0.50/sensor/filament_weight
```
```json
{ "id": "sensor-filament_weight", "value": 217.4, "state": "217.4 g" }
```

### Schalter / Button-Aktionen

```http
POST /button/<button_id>/press
```

| Button-ID | Beschreibung |
| :--- | :--- |
| `tare_scale` | Waage tarieren (nullen) |
| `sync_spoolman` | Spoolman-Sync manuell auslösen |
| `sync_filaman` | FilaMan-Sync manuell auslösen |

**Beispiel:**
```bash
curl -X POST http://192.168.0.50/button/tare_scale/press
```

---

## Home Assistant Integration

Das Gerät integriert sich nativ über die **ESPHome-Integration** in Home Assistant (API-Schlüssel erforderlich).

### Einrichtung

1. In Home Assistant: **Einstellungen → Geräte & Dienste → Integration hinzufügen → ESPHome**.
2. IP-Adresse des Geräts eingeben.
3. API-Schlüssel aus der `example_config.yaml` eingeben.

### Verfügbare Entitäten

| Entität | Typ | Beschreibung |
| :--- | :--- | :--- |
| `sensor.filament_weight` | Sensor | Aktuelles Nettogewicht (g) |
| `sensor.filament_remaining` | Sensor | Restfilament (%) |
| `button.tare_scale` | Button | Waage tarieren |
| `button.sync_spoolman` | Button | Spoolman-Sync auslösen |
| `switch.winder_enabled` | Switch | Wickler aktivieren/deaktivieren |
| `number.winder_speed` | Number | Wicklergeschwindigkeit (0–100 %) |

### Beispiel-Automation

```yaml
automation:
  - alias: "Benachrichtigung bei wenig Filament"
    trigger:
      - platform: numeric_state
        entity_id: sensor.filament_remaining
        below: 15
    action:
      - service: notify.mobile_app
        data:
          message: "⚠️ Filament unter 15 % – bitte Spule wechseln!"
```

---

## Spoolman API

[Spoolman](https://github.com/Donkie/Spoolman) ist ein selbst gehosteter Filament-Inventarmanager. Die Waage überträgt Gewichtsaktualisierungen automatisch nach jeder stabilen Messung.

### Konfiguration

```yaml
spoolman_ip: "192.168.0.1:7912"
```

### Funktionsweise

Nach einem stabilen Messwert sendet die Waage einen HTTP-PATCH-Request an die Spoolman API:

```http
PATCH http://<spoolman_ip>/api/v1/spool/<spool_id>
Content-Type: application/json

{ "remaining_weight": 217.4 }
```

Die aktive `spool_id` wird über das Gerätemenü ausgewählt oder beim Start von Spoolman synchronisiert.

### Manueller Sync

```bash
curl -X POST http://192.168.0.50/button/sync_spoolman/press
```

---

## FilaMan API

[FilaMan](https://docu.filaman.app/) ist ein webbasiertes Open-Source-System zur Filamentverwaltung mit Unterstützung für OctoPrint, Klipper/Moonraker und Home Assistant.

### Konfiguration

```yaml
filaman_ip: "192.168.0.1:8083"
```

### Funktionsweise

Die Waage überträgt das aktuelle Spulengewicht nach jeder stabilen Messung an FilaMan.

```http
POST http://<filaman_ip>/api/scale
Content-Type: application/json

{ "weight": 217.4, "spool_id": "abc123" }
```

> ℹ️ Der genaue Endpunkt kann je nach FilaMan-Version abweichen. Aktuelle Infos in der [FilaMan-Dokumentation](https://docu.filaman.app/).

### Home Assistant Add-on

FilaMan kann als Home Assistant Add-on über [ha-filaman-system](https://github.com/netscout2001/ha-filaman-system) installiert werden. Interne HA-IP und konfigurierten Port verwenden.