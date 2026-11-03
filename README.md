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

