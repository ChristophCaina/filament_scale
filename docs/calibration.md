# ⚖ Calibration Guide

> 🇩🇪 [Deutsche Version weiter unten](#-kalibrierungsanleitung)

---

This guide explains how to calibrate the load cell so the scale reads accurate weights.

Calibration is done entirely via **Home Assistant** — no reflash or YAML editing required. All values are stored directly on the device and survive reboots.

---

## Prerequisites

- The scale is flashed and connected to your network.
- The device has been adopted in your ESPHome dashboard or Home Assistant.
- You have a known reference weight (e.g. a 1 kg spool, a calibration weight, or any object with a known mass).
- The scale platform is empty and stable.

---

## Step 1 – Tare (Zero the Scale)

1. Remove everything from the scale platform.
2. In Home Assistant, go to your **Filament Scale device** → **Controls**.
3. Press the **"Tare Scale"** button.

The `Weight` sensor should now show `0.0 g`.

---

## Step 2 – Read the Raw Value

1. Place your known reference weight on the platform (e.g. exactly **1000 g**).
2. In Home Assistant, go to your **Filament Scale device** → **Sensors**.
3. Note the current value of **"Scale Raw"** — e.g. `412380`.

> ℹ️ The `Scale Raw` sensor shows the unprocessed integer value from the HX711. It is visible as a diagnostic entity in Home Assistant.

---

## Step 3 – Calculate the Calibration Factor

```
Calibration Factor = Raw Value / Known Weight in grams
Calibration Factor = 412380 / 1000 = 412.38
```

---

## Step 4 – Enter the Calibration Factor

1. In Home Assistant, go to your **Filament Scale device** → **Controls**.
2. Find the **"Calibration Factor"** number entity.
3. Enter your calculated value (e.g. `412.38`).

The `Weight` sensor updates immediately — no reflash needed. ✅

---

## Step 5 – Set Spool Weights (Optional)

For accurate **Filament Remaining %** calculation, set your spool weights:

| Entity | Description | Typical Value |
| :--- | :--- | :--- |
| **Empty Spool Weight** | Weight of the empty spool (no filament) | 200 g |
| **Total Filament Weight** | Total filament weight when full | 1000 g |

These are also number entities in Home Assistant and stored on the device.

---

## Step 6 – Verify Accuracy

1. Tare the scale (empty platform).
2. Place the reference weight on the platform.
3. The `Weight` sensor should now show the correct weight ± 1–2 g.
4. For higher accuracy, use multiple reference weights and average the factor.

---

## Tips & Troubleshooting

| Issue | Possible Cause | Solution |
| :--- | :--- | :--- |
| Value drifts over time | Temperature changes | Re-tare after warm-up (~5 min) |
| Reading is off by a fixed offset | Tare not performed | Tare with empty platform |
| Reading is off by a percentage | Wrong calibration factor | Recalculate and update factor |
| Unstable / noisy reading | Mechanical vibration | Ensure stable surface, check cell mounting |
| Value jumps to max | Wiring issue | Check DT/SCK pin connections |
| `Scale Raw` shows `NaN` | HX711 not connected | Check DT/SCK wiring |

---
---

# ⚖ Kalibrierungsanleitung

Die Kalibrierung erfolgt vollständig über **Home Assistant** — kein erneutes Flashen oder Bearbeiten von YAML-Dateien erforderlich. Alle Werte werden direkt auf dem Gerät gespeichert und bleiben auch nach einem Neustart erhalten.

---

## Voraussetzungen

- Die Waage ist geflasht und mit dem Netzwerk verbunden.
- Das Gerät wurde im ESPHome-Dashboard oder in Home Assistant adoptiert.
- Ein bekanntes Referenzgewicht liegt bereit (z. B. eine 1-kg-Spule, ein Kalibriergewicht oder ein Objekt mit bekannter Masse).
- Die Wägeplattform ist leer und stabil.

---

## Schritt 1 – Tarieren (Nullen der Waage)

1. Alles von der Wägeplattform entfernen.
2. In Home Assistant zum **Filament Scale Gerät** → **Steuerelemente** navigieren.
3. Den Button **"Tare Scale"** drücken.

Der Sensor `Weight` zeigt nun `0.0 g`.

---

## Schritt 2 – Rohwert ablesen

1. Referenzgewicht auf die Plattform legen (z. B. genau **1000 g**).
2. In Home Assistant zum **Filament Scale Gerät** → **Sensoren** navigieren.
3. Den aktuellen Wert von **"Scale Raw"** notieren — z. B. `412380`.

> ℹ️ Der Sensor `Scale Raw` zeigt den unverarbeiteten Integer-Wert des HX711. Er ist als Diagnose-Entität in Home Assistant sichtbar.

---

## Schritt 3 – Kalibrierungsfaktor berechnen

```
Kalibrierungsfaktor = Rohwert / bekanntes Gewicht in Gramm
Kalibrierungsfaktor = 412380 / 1000 = 412.38
```

---

## Schritt 4 – Kalibrierungsfaktor eintragen

1. In Home Assistant zum **Filament Scale Gerät** → **Steuerelemente** navigieren.
2. Die Number-Entität **"Calibration Factor"** suchen.
3. Den berechneten Wert eintragen (z. B. `412.38`).

Der Sensor `Weight` aktualisiert sich sofort — kein erneutes Flashen nötig. ✅

---

## Schritt 5 – Spulengewichte festlegen (Optional)

Für eine genaue **Restfilament-%**-Berechnung die Spulengewichte eintragen:

| Entität | Beschreibung | Typischer Wert |
| :--- | :--- | :--- |
| **Empty Spool Weight** | Gewicht der leeren Spule (ohne Filament) | 200 g |
| **Total Filament Weight** | Gesamtfilamentgewicht bei voller Spule | 1000 g |

Diese sind ebenfalls Number-Entitäten in Home Assistant und werden auf dem Gerät gespeichert.

---

## Schritt 6 – Genauigkeit überprüfen

1. Waage erneut tarieren (leere Plattform).
2. Referenzgewicht auf die Plattform legen.
3. Der Sensor `Weight` sollte nun das korrekte Gewicht ± 1–2 g anzeigen.
4. Für höhere Genauigkeit mehrere Referenzgewichte verwenden und den Faktor mitteln.

---

## Tipps & Fehlerbehebung

| Problem | Mögliche Ursache | Lösung |
| :--- | :--- | :--- |
| Wert driftet über die Zeit | Temperaturschwankungen | Nach Aufwärmzeit (~5 min) neu tarieren |
| Wert um festen Offset falsch | Tarierung nicht durchgeführt | Mit leerer Plattform tarieren |
| Wert prozentuell falsch | Falscher Kalibrierungsfaktor | Faktor neu berechnen und eintragen |
| Instabiler / verrauschter Wert | Mechanische Vibrationen | Stabilen Untergrund sicherstellen, Zellenhalterung prüfen |
| Wert springt auf Maximum | Verkabelungsfehler | DT/SCK-Pinverbindungen prüfen |
| `Scale Raw` zeigt `NaN` | HX711 nicht verbunden | DT/SCK-Verkabelung prüfen |
