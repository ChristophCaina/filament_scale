# 🔌 API Documentation

> 🇩🇪 [Deutsche Version weiter unten](#-api-dokumentation)

---

This document describes all external interfaces of the ESPHome Filament Scale: the native ESPHome REST API, Home Assistant integration, and the unified sync logic for Spoolman and FilaMan.

---

## ESPHome REST API

ESPHome exposes a built-in REST API that allows reading sensor values and triggering actions without Home Assistant.

**Base URL:** `http://<device-ip>/`

### Sensor States

```http
GET /sensor/<sensor_id>
```

| Sensor ID | Description |
| :--- | :--- |
| `filament_weight` | Current net weight in grams |
| `filament_remaining_pct` | Calculated remaining filament in % |

**Example:**
```bash
curl http://192.168.0.50/sensor/filament_weight
```
```json
{ "id": "sensor-filament_weight", "value": 217.4, "state": "217.4 g" }
```

### Button Actions

```http
POST /button/<button_id>/press
```

| Button ID | Description |
| :--- | :--- |
| `tare_scale` | Zero/tare the scale |
| `sync` | Manually trigger sync to active backend |

**Example:**
```bash
curl -X POST http://192.168.0.50/button/sync/press
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
| `sensor.filament_remaining_pct` | Sensor | Remaining filament (%) |
| `button.tare_scale` | Button | Tare the scale |
| `button.sync` | Button | Trigger sync to active backend |
| `switch.winder_enabled` | Switch | Enable/disable winder motor |
| `number.winder_speed` | Number | Winder speed (0–100%) |

### Example Automation

```yaml
automation:
  - alias: "Notify when filament running low"
    trigger:
      - platform: numeric_state
        entity_id: sensor.filament_remaining_pct
        below: 15
    action:
      - service: notify.mobile_app
        data:
          message: "⚠️ Filament below 15% – please reload spool!"
```

---

## Sync Logic — FilaMan & Spoolman

The scale uses a **single unified sync mechanism** (`sync.yaml`) that automatically selects the correct backend at boot. Both FilaMan and Spoolman share the same Spoolman-compatible REST API, so the HTTP call is identical — only the base URL differs.

### How FilaMan and Spoolman relate

FilaMan provides a **Spoolman API Plugin** that exposes a fully Spoolman-compatible REST API under `/spoolman/api/v1/`. External tools can use FilaMan as a drop-in replacement for Spoolman without reconfiguration.

This means: if FilaMan is configured, the scale talks to FilaMan using the Spoolman API. FilaMan then handles synchronisation with Spoolman internally — no duplicate pushes needed.

### Priority Logic

```
Boot
 │
 ├─► filaman_ip configured?
 │    ├─► Yes → probe http://<filaman_ip>/spoolman/api/v1/health
 │    │         ├─► HTTP 200 → use FilaMan as backend ✅
 │    │         └─► Error   → fallback to Spoolman ⚠️
 │    │
 │    └─► No → use Spoolman directly
 │
 └─► Weight push (PATCH /spool/<id>) → active backend
```

### Configuration

Set the IPs in `example_config.yaml`:

```yaml
substitutions:
  spoolman_ip: "192.168.0.1:7912"   # always set this
  filaman_ip:  "192.168.0.1:8083"   # leave empty to skip FilaMan
```

To use **Spoolman only**, leave `filaman_ip` empty:
```yaml
  filaman_ip: ""
```

To use **FilaMan only** (with FilaMan→Spoolman sync handled by FilaMan):
```yaml
  filaman_ip: "192.168.0.1:8083"
  spoolman_ip: ""   # not needed if FilaMan handles Spoolman internally
```

### The sync call (identical for both backends)

```http
PATCH http://<backend>/api/v1/spool/<spool_id>
Content-Type: application/json

{ "remaining_weight": 217.4 }
```

| Backend | Effective URL |
| :--- | :--- |
| FilaMan | `http://<filaman_ip>/spoolman/api/v1/spool/<id>` |
| Spoolman | `http://<spoolman_ip>/api/v1/spool/<id>` |

### HTTP Response Codes

| Code | Meaning | Device reaction |
| :--- | :--- | :--- |
| `200` | Sync successful | ✅ Confirm beep |
| `404` | Spool ID not found | ❌ Error beep, log warning |
| Other | Server error | ❌ Error beep, log warning |

### Manual Sync

```bash
curl -X POST http://192.168.0.50/button/sync/press
```

---

## NFC / RFID Tag Integration

> 🔲 Planned — not yet implemented

When an NFC tag is scanned, the scale reads the spool ID from the tag and automatically pushes the current weight to the active backend (FilaMan or Spoolman).

If no FilaMan is configured and a spool is identified via Spoolman, the Spoolman spool ID can be written back to the NFC tag for future auto-identification.

---
---

# 🔌 API-Dokumentation

Dieses Dokument beschreibt alle externen Schnittstellen der ESPHome Filamentwaage: die native ESPHome REST API, die Home Assistant Integration sowie die einheitliche Sync-Logik für Spoolman und FilaMan.

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
| `filament_remaining_pct` | Berechnetes Restfilament in % |

**Beispiel:**
```bash
curl http://192.168.0.50/sensor/filament_weight
```
```json
{ "id": "sensor-filament_weight", "value": 217.4, "state": "217.4 g" }
```

### Button-Aktionen

```http
POST /button/<button_id>/press
```

| Button-ID | Beschreibung |
| :--- | :--- |
| `tare_scale` | Waage tarieren (nullen) |
| `sync` | Sync zum aktiven Backend manuell auslösen |

**Beispiel:**
```bash
curl -X POST http://192.168.0.50/button/sync/press
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
| `sensor.filament_remaining_pct` | Sensor | Restfilament (%) |
| `button.tare_scale` | Button | Waage tarieren |
| `button.sync` | Button | Sync zum aktiven Backend auslösen |
| `switch.winder_enabled` | Switch | Wickler aktivieren/deaktivieren |
| `number.winder_speed` | Number | Wicklergeschwindigkeit (0–100 %) |

### Beispiel-Automation

```yaml
automation:
  - alias: "Benachrichtigung bei wenig Filament"
    trigger:
      - platform: numeric_state
        entity_id: sensor.filament_remaining_pct
        below: 15
    action:
      - service: notify.mobile_app
        data:
          message: "⚠️ Filament unter 15 % – bitte Spule wechseln!"
```

---

## Sync-Logik — FilaMan & Spoolman

Die Waage verwendet einen **einheitlichen Sync-Mechanismus** (`sync.yaml`), der beim Start automatisch das richtige Backend erkennt. Sowohl FilaMan als auch Spoolman verwenden dieselbe Spoolman-kompatible REST API — der HTTP-Aufruf ist identisch, nur die Basis-URL unterscheidet sich.

### Wie FilaMan und Spoolman zusammenhängen

FilaMan bietet ein **Spoolman API Plugin**, das eine vollständig Spoolman-kompatible REST API unter `/spoolman/api/v1/` bereitstellt. Externe Tools können FilaMan damit als direkten Ersatz für Spoolman verwenden. Ist FilaMan konfiguriert, spricht die Waage über die Spoolman-API mit FilaMan — FilaMan übernimmt die interne Synchronisierung mit Spoolman. Kein doppelter Push nötig.

### Priorisierungslogik

```
Start
 │
 ├─► filaman_ip konfiguriert?
 │    ├─► Ja → http://<filaman_ip>/spoolman/api/v1/health prüfen
 │    │         ├─► HTTP 200 → FilaMan als Backend verwenden ✅
 │    │         └─► Fehler   → Fallback auf Spoolman ⚠️
 │    │
 │    └─► Nein → Spoolman direkt verwenden
 │
 └─► Gewicht-Push (PATCH /spool/<id>) → aktives Backend
```

### Konfiguration

IPs in der `example_config.yaml` eintragen:

```yaml
substitutions:
  spoolman_ip: "192.168.0.1:7912"   # immer angeben
  filaman_ip:  "192.168.0.1:8083"   # leer lassen um FilaMan zu überspringen
```

**Nur Spoolman** — `filaman_ip` leer lassen:
```yaml
  filaman_ip: ""
```

**Nur FilaMan** (FilaMan → Spoolman Sync läuft intern über FilaMan):
```yaml
  filaman_ip: "192.168.0.1:8083"
  spoolman_ip: ""
```

### Der Sync-Aufruf (identisch für beide Backends)

```http
PATCH http://<backend>/api/v1/spool/<spool_id>
Content-Type: application/json

{ "remaining_weight": 217.4 }
```

| Backend | Effektive URL |
| :--- | :--- |
| FilaMan | `http://<filaman_ip>/spoolman/api/v1/spool/<id>` |
| Spoolman | `http://<spoolman_ip>/api/v1/spool/<id>` |

### HTTP-Antwortcodes

| Code | Bedeutung | Reaktion des Geräts |
| :--- | :--- | :--- |
| `200` | Sync erfolgreich | ✅ Bestätigungs-Beep |
| `404` | Spulen-ID nicht gefunden | ❌ Fehler-Beep, Log-Warnung |
| Sonstige | Serverfehler | ❌ Fehler-Beep, Log-Warnung |

### Manueller Sync

```bash
curl -X POST http://192.168.0.50/button/sync/press
```

---

## NFC / RFID Tag Integration

> 🔲 Geplant — noch nicht implementiert

Wird ein NFC-Tag gescannt, liest die Waage die Spulen-ID vom Tag und überträgt das aktuelle Gewicht automatisch an das aktive Backend (FilaMan oder Spoolman).

Ist kein FilaMan konfiguriert und eine Spule über Spoolman identifiziert, kann die Spoolman-Spulen-ID zur künftigen automatischen Erkennung auf den NFC-Tag zurückgeschrieben werden.