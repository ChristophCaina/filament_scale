# ⚖️ Calibration Guide

> 🇩🇪 [Deutsche Version weiter unten](#-kalibrierungsanleitung)

---

This guide explains how to calibrate the load cell so the scale reads accurate weights.

---

## Prerequisites

- The scale is flashed and connected to your network.
- You have a known reference weight (e.g. a 1 kg spool, a calibration weight, or any object with a known mass).
- The scale platform is empty and stable during tare/calibration.

---

## Step 1 – Tare (Zero the Scale)

Before calibrating, the scale must be zeroed with an empty platform:

1. Remove everything from the scale platform.
2. Navigate to **Menu → 1. Tare** on the display, or trigger via Home Assistant / REST API:

```bash
curl -X POST http://192.168.0.50/button/tare_scale/press
```

The display should now show `0 g`.

---

## Step 2 – Find the Raw Calibration Factor

The HX711 load cell returns raw integer values that need to be mapped to real-world grams. This is done via a **calibration factor** in the `waegezelle` package.

1. Place your known reference weight on the platform (e.g. exactly **1000 g**).
2. Note the raw value displayed on screen (if enabled) or read it via the ESPHome logs:

```
[sensor] 'Raw Scale Value': 412380
```

3. Calculate the calibration factor:

```
calibration_factor = raw_value / known_weight_in_grams
calibration_factor = 412380 / 1000 = 412.38
```

---

## Step 3 – Apply the Calibration Factor

Open your `example_config.yaml` and update the substitution:

```yaml
substitutions:
  scale_calibration_factor: "412.38"
```

Then recompile and flash the firmware (**Install → Wirelessly** via OTA is sufficient).

---

## Step 4 – Verify Accuracy

1. Tare the scale again (empty platform).
2. Place the reference weight on the platform.
3. The display should now show the correct weight ± 1–2 g.
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

---
---

# ⚖️ Kalibrierungsanleitung

Diese Anleitung erklärt, wie die Wägezelle kalibriert wird, damit die Waage genaue Gewichte anzeigt.

---

## Voraussetzungen

- Die Waage ist geflasht und mit dem Netzwerk verbunden.
- Ein bekanntes Referenzgewicht liegt bereit (z. B. eine 1-kg-Spule, ein Kalibriergewicht oder ein Objekt mit bekannter Masse).
- Die Wägeplattform ist während der Tarierung/Kalibrierung leer und stabil.

---

## Schritt 1 – Tarieren (Nullen der Waage)

Vor der Kalibrierung muss die Waage mit leerer Plattform genullt werden:

1. Alles von der Wägeplattform entfernen.
2. Im Display navigieren: **Menü → 1. Tare** – oder per Home Assistant / REST API auslösen:

```bash
curl -X POST http://192.168.0.50/button/tare_scale/press
```

Das Display zeigt nun `0 g`.

---

## Schritt 2 – Rohen Kalibrierungsfaktor ermitteln

Der HX711 liefert rohe Integer-Werte, die auf echte Gramm-Werte abgebildet werden müssen. Dies geschieht über einen **Kalibrierungsfaktor** im `waegezelle`-Paket.

1. Referenzgewicht auf die Plattform legen (z. B. genau **1000 g**).
2. Den angezeigten Rohwert notieren (sofern aktiviert) oder aus den ESPHome-Logs ablesen:

```
[sensor] 'Raw Scale Value': 412380
```

3. Kalibrierungsfaktor berechnen:

```
Kalibrierungsfaktor = Rohwert / bekanntes Gewicht in Gramm
Kalibrierungsfaktor = 412380 / 1000 = 412.38
```

---

## Schritt 3 – Kalibrierungsfaktor eintragen

In der `example_config.yaml` die Substitution aktualisieren:

```yaml
substitutions:
  scale_calibration_factor: "412.38"
```

Anschließend Firmware neu kompilieren und flashen (**Install → Wirelessly** per OTA genügt).

---

## Schritt 4 – Genauigkeit überprüfen

1. Waage erneut tarieren (leere Plattform).
2. Referenzgewicht auf die Plattform legen.
3. Das Display sollte nun das korrekte Gewicht ± 1–2 g anzeigen.
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