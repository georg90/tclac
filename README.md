# 🌀 TCL Air Conditioner Integration for Home Assistant

> 🇩🇪 Deutsche Fassung auf Basis von [sorz2122/tclac](https://github.com/sorz2122/tclac), zusammengeführt mit dem aktuellen Stand des Originals [I-am-nightingale/tclac](https://github.com/I-am-nightingale/tclac).  
> Einfaches DIY-Projekt mit ESP32 + USB-Kabel. Keine Cloud nötig.

---

## 🛠️ Was du brauchst

- **ESP32** (z. B. ESP32-C3, WROOM32, NodeMCU)
- **USB-A-Stecker oder -Kabel**  
  👉 Ich habe dieses verwendet: [AliExpress-Link](https://www.aliexpress.com/item/1005005776162012.html)
- **Home Assistant mit ESPHome (ab Version 2026.4.0)**

---

## 🔌 Verkabelung

| USB-A Pin | Kabelfarbe | → ESP32 Pin |
|-----------|------------|--------------|
| GND       | Schwarz    | VIN/VCC      |
| D+        | Grün       | GND          |
| D-        | Grau       | RXD          |
| VBUS      | Rot        | TXD          |

### 🔍 Beispielbilder
(Beachte, dass ich hier nicht auf die Farben der Kabel geachtet habe. Die Farben in der Tabelle entsprechen jedoch in der Regel gängigen USB-A-Kabeln, die man einfach abschneiden kann.)

<img src="https://github.com/user-attachments/assets/9b674e06-41ca-4bcf-b09b-691a5fbd8545" width="400"/>
<br/>

![Wiring Example 2](https://github.com/user-attachments/assets/e30fadd9-19cd-47ec-baab-86f8a80410f6)

![7480a856c7839044d7a04292d352b709a2155c07_2_296x500](https://github.com/user-attachments/assets/5b3ccbb8-eb62-4743-8d05-f88a9b986743)

---

## 🧠 Einrichtung in Home Assistant

> Die Lösung basiert auf **ESPHome** und funktioniert nur mit Home Assistant.

### 1. ESPHome installieren

- In Home Assistant unter **Einstellungen → Add-ons → ESPHome** installieren

### 2. Neues Gerät erstellen

- Im ESPHome-Dashboard → "New Device"
- Deinen ESP32-Typ auswählen, z. B. `esp32-c3-devkitm-1` oder `nodemcu-32s`

### 3. Konfiguration einfügen

#### Option A: Einfache Konfiguration
[📄 Sample_conf.yaml](https://github.com/georg90/tclac/blob/master/Sample_conf.yaml)

#### Option B: Erweiterte Konfiguration
[📄 TCL-Conditioner.yaml](https://github.com/georg90/tclac/blob/master/TCL-Conditioner.yaml)

📝 **Wichtig:**  
- WLAN-Daten, Gerätename etc. anpassen  
- Kommentare im YAML helfen beim Einrichten

### 4. Auf ESP32 flashen

- USB-Kabel anschließen oder OTA (Over-the-Air) verwenden

---

## ✅ Kompatible Klimaanlagen

Diese Modelle wurden erfolgreich getestet (teils mit Löten):

- **Axioma:** ASX09H1 / ASB09H1
- **Ballu:** BSAI-12HN1_15Y, Discovery DC BSVI-07HN8 / BSVI-09HN8 / BSVI-12HN8
- **Daichi:** AIR20AVQ1/AIR20FV1, AIR25AVQS1R-1/AIR25FVS1R-1, AIR35AVQS1R-1/AIR35FVS1R-1, DA35EVQ1-1/DF35EV1-1
- **Dantex:** RK-12SATI / RK-12SATIE
- **Ecostar:** Radium KVS-RAD09CH
- **iFFALCON:** F1 18
- **Royal Clima:** Gloria Inverter, Pandora RC-PDC28HN
- **Tesla:** TT27TP61S-0932IAWUV
- **TCL:** ELI ONF 12, Liferise ONF 09, TAC-CT09INV/R, One Inverter TACM-09HRID/E1 (evtl. andere Pinbelegung), TAC-07CHSA/TPG-W, TAC-09CHSA/TPG, TAC-09CHSA/DSEI-W, TAC-09HRID/E1, TAC-12CHSA/TPG, TAC-12CHSA/TPGI, TAC-12CHDA, TAC-XAL24I, TPG31IHB
- ...und ähnliche Modelle

Die Komponente versteht Statusmeldungen der Klimaanlage mit 61, 65 und 68 Byte Länge. Ausgiebig getestet ist bisher nur die 61-Byte-Variante.

⚠️ **Hinweis:**  
Auch wenn die Modellbezeichnung passt, kann es Unterschiede geben (kein USB-Anschluss, kein UART auf der Platine etc.).

---

## 🙏 Herkunft und Dank

- Original-Komponente: [I-am-nightingale/tclac](https://github.com/I-am-nightingale/tclac) (Соловей с паяльником), Projektartikel [auf Dzen](https://dzen.ru/a/ZmdoyUNswXWnulhg)
- Deutsche Übersetzung und Anleitung: [sorz2122/tclac](https://github.com/sorz2122/tclac) – Unterstützung: https://buymeacoffee.com/sorz2122
- Variante für MQTT statt Home Assistant: [pavel211/TCL-TAC-07-WiFi](https://github.com/pavel211/TCL-TAC-07-WiFi)

---

## 🔧 Erweiterte Konfiguration per Remote Package

Du kannst die Konfiguration modular laden:

```yaml
packages:
  remote_package:
    url: https://github.com/georg90/tclac.git
    ref: master
    files:
      - packages/core.yaml   # Hauptmodul
      # - packages/leds.yaml # Optional
    refresh: 30s

---

## ➕ Optionale Pakete

Alle Pakete werden unter `files:` eingetragen und müssen exakt auf derselben Einrückungsebene wie `packages/core.yaml` stehen.

| Paket | Funktion |
|-------|----------|
| `packages/core.yaml` | Pflicht – Klimasteuerung, Schalter und Auswahllisten |
| `packages/leds.yaml` | LEDs für Senden/Empfangen (Pins über `receive_led` / `transmit_led`) |
| `packages/bad_connect.yaml` | Sendet jeden Befehl 3-mal im Abstand von 200 ms – hilft bei unzuverlässiger Verbindung, z. B. ohne Pegelwandler 3,3 V → 5 V |
| `packages/uart_speed.yaml` | Auswahlliste „Baud rate“ in den Geräteeinstellungen, für Klimaanlagen mit anderer UART-Geschwindigkeit als 9600 |
| `packages/screen.yaml` | Anzeige des Zustands auf einem OLED-Display (SSD1306) |

Beispiel mit 3-fachem Senden:

```yaml
packages:
  remote_package:
    url: https://github.com/georg90/tclac.git
    ref: master
    files:
    # v – Zeilen müssen exakt hier ausgerichtet sein
      - packages/core.yaml
      - packages/bad_connect.yaml
    refresh: 30s
```

---

## 🌐 Feste IP-Adresse

Standardmäßig holt sich das Modul die IP-Adresse per DHCP. Für eine feste Adresse ans Ende der Konfiguration anhängen:

```yaml
wifi:
  manual_ip:
    static_ip: 192.168.1.4
    gateway: 192.168.1.1
    subnet: 255.255.255.0
```

---

## 🆕 Änderungen gegenüber sorz2122

Übernommen aus dem Original (Stand August 2026):

- Unterstützung für 65- und 68-Byte-Statusmeldungen
- Schutz vor Pufferüberlauf und abgebrochenen Rahmen bei gestörter Leitung
- Befehle werden erst gesendet, wenn die Leitung frei ist („erst hören, dann senden“), ohne die Hauptschleife zu blockieren
- Modus wird auch bei ausgeschaltetem Display korrekt erkannt; Display-Zustand wird vom Gerät übernommen
- Raumtemperatur mit Nachkommastellen
- Schutz vor Echo-Schleifen durch Integrationen, die den Zustand zurückspiegeln
- „Auto“ wird als **Heizen/Kühlen** (`HEAT_COOL`) an Home Assistant gemeldet
- Funktioniert mit den Frameworks Arduino **und** ESP-IDF
- Neue Pakete `bad_connect.yaml` und `uart_speed.yaml`
- Mindestversion ESPHome 2026.4.0

### Telemetrie-Sensoren

| Sensor | Type | Source byte(s) | Notes |
|--------|------|---------------|-------|
| `turbo_sensor` | `binary_sensor` | `dataRX[7]` bit 7 | Turbo active (set by remote or unit) |
| `compressor_running` | `binary_sensor` | `dataRX[46]` | 0 = idle, > 0 = running |
| `indoor_coil_temperature` | `sensor` | `dataRX[30]` | Indoor coil (T2): `(dataRX[30]·4 − 200) / 10` °C, 0.4 K steps |
| `outdoor_temperature` | `sensor` | `dataRX[35]` | Outdoor ambient at the outdoor unit (T4): `dataRX[35] − 20` °C; NaN when 0 |
| `evaporator_temperature` | `sensor` | `dataRX[36]` | Outdoor coil (T3/OPT, evaporator in heating mode): `dataRX[36] − 20` °C, 1 K steps (0 = −20 °C, lower limit). Versions before c10e294 wrongly combined bytes 36–37 as `(raw/374−32)/1.8` |
| `discharge_temperature` | `sensor` | `dataRX[37]` | Compressor discharge (Tp/ODT): `dataRX[37] − 20` °C; NaN when 0 |
| `outdoor_fan_speed` | `sensor` | `dataRX[38]` | Outdoor fan, raw value (≈ rpm/10?), 0 = off (standstill and during defrost). Previously mislabelled as `compressor_temperature` |
| `compressor_frequency` | `sensor` | `dataRX[39]` | Hz; 0 = compressor stopped |
| `supply_voltage` | `sensor` | `dataRX[45]` | Mains voltage in V; only valid while compressor runs |
| `diagnostic_hex` | `text_sensor` | `dataRX[34–59]` | Raw hex dump of not-yet-decoded bytes for further research |
| `raw_bytes` | list of `sensor` | any `dataRX[n]` | Publishes the raw value of any frame byte (`byte: n`, counted from 0xBB) – for decoding further fields, e.g. byte 40 (EEV?) or 46 (current?) |

Room temperature (`current_temperature`) now uses the formula of the original TCL Wi-Fi module: `(dataRX[17]·4 + ((dataRX[18] >> 2) & 3) − 200) / 10` °C (0.1 K steps). The former `((b17<<8|b18)/374 − 32)/1.8` was an approximation (error up to ~0.7 K away from 20 °C).

**Breaking changes:** `compressor_temperature` was removed (byte 38 is the outdoor fan → `outdoor_fan_speed`), `byte37_temperature` was renamed to `discharge_temperature` (now with offset −20). ESPHome reports both old keys with a hint.

#### Status frame byte map (61-byte frame, index 0 = 0xBB)

| Byte | Meaning | Formula / note | Confidence |
|---|---|---|---|
| 17–18 | room temperature | see above | high (original module firmware) |
| 30 | indoor coil T2 | `(b·4 − 200)/10` | medium |
| 34 | indoor fan? | raw | low |
| 35 | outdoor ambient T4 | `b − 20` | high |
| 36 | outdoor coil T3/OPT | `b − 20` | very high (verified at standstill against an outdoor sensor: 0.0 K median) |
| 37 | discharge Tp | `b − 20` | high (meaning), medium (offset) |
| 38 | outdoor fan | raw, 0 = off | high |
| 39 | compressor frequency | Hz | very high |
| 40 | EEV opening? | raw | low |
| 41–44 | error bits | – | medium |
| 45 | mains voltage | V | very high |
| 46 | outdoor current? / compressor active | raw | low |
| 51 / 52 | vertical / horizontal louver | bits 0–2 position, 3–5 range | high |
| 55 | low nibble + 16 = "constant temperature" | – | medium |

Sources: own measurements (14 days of InfluxDB data vs. an independent outdoor sensor) and community reverse engineering: [kralonur/tcl-ac-original-module-esphome](https://github.com/kralonur/tcl-ac-original-module-esphome) (original TCL WL6U module firmware), [adaasch/AC-hack](https://github.com/adaasch/AC-hack), [E-t0m/esphome-local-ac-control](https://github.com/E-t0m/esphome-local-ac-control), TCL inverter service manual (defrost: outdoor fan off during defrost).

