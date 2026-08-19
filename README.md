# Galvanically Isolated DMX-512 Transceiver Board (Rev 1.0)

[![Hardware License: CERN-OHL-P-2.0](https://img.shields.io/badge/License-CERN--OHL--P--2.0-blue.svg)](https://spdx.org/licenses/CERN-OHL-P-2.0.html)
[![KiCad Version](https://img.shields.io/badge/KiCad-v8%20%2F%20v9%20%2F%20v10-brightgreen.svg)](https://www.kicad.org/)
[![Hardware Revision](https://img.shields.io/badge/Revision-Rev%201.0-orange.svg)](#)

A compact and galvanically isolated interface board for **DMX-512 (RS-485)** applications using standard 3-Pin XLR jacks or JST-XH 2.50 mm headers. The board enables transmitting and receiving DMX signals via microcontrollers (e.g., ESP32, Arduino, Raspberry Pi, RP2040, and others) while completely protecting host logic from ground loops, power surges, and potential differences.

---

## Key Features

- **Full galvanic isolation:**
  - **Power Isolation:** Isolated DC/DC converter `B0505S-1WR3` (for 5V host logic) or `B0305S-1WR3` (for 3.3V host logic), delivering 5V isolated.
  - **Signal Isolation:** The host lines are isolated by an `ADuM1201AR` dual-channel magnetic digital isolator.
- **ESD and surge protection:**
  - `SM712` TVS diode arrays on DMX_IN and DMX_OUT lines protecting transceivers against ESD and faults on the RS-485 bus.
- **Flexible host logic voltage (3.3V or 5V):**
  - Seamlessly interfaces with 3.3V or 5V microcontrollers by soldering the appropriate DC/DC converter.
- **Dedicated hardware channels:**
  - Both the transmit and receive lines utilize a dedicated `MAX485E` for signal conversion.
  - If the board is only used for DMX transmitting or receiving, the unused transceiver can be left unpopulated.
- **Configurable operating modes via solder pads (`JP1` and `JP2`):**
  - **DMX IN + THRU:** DMX_IN is directly bridged to DMX_OUT while the board can simultaneously output the incoming DMX stream to the host via the `TX` pin. To configure this mode, solder pads `JP1` (Pin 1 - Pin 2) and `JP2` (Pin 1 - Pin 2) must be bridged. In this mode, the `MAX485E` transceiver for the DMX OUT line is not used.
  - **DMX IN + OUT:** DMX_OUT is driven actively by the host MCU via the `RX` input pin. To configure this mode, solder pads `JP1` (Pin 2 - Pin 3) and `JP2` (Pin 2 - Pin 3) must be bridged. In this mode, both `MAX485E` transceivers are used.
- **Versatile connectivity options:**
  - Neutrik 3-Pin XLR connectors (`NC3MAAH` male and `NC3FAAH` female, `J1` and `J2`) can be soldered directly to the board.
  - Both XLR ports can be broken off at the mouse-bites to use the alternative `JST-XH 2.50 mm` headers (`J4`, `J5`) for internal wiring.
- **Mounting:**
  - The board comes with four integrated M2 mounting holes.

---

## Power Supply & Logic Level Selection

The board supports both **3.3V** and **5V** host microcontrollers. The `ADuM1201AR` isolator automatically operates at the logic voltage supplied to `VCC` on `J3`, so the `TX` output pin on `J3` automatically matches this voltage (3.3V or 5V). The isolated secondary side always runs at 5V, ensuring full RS-485 and MAX485E standard compliance.

To match your host logic voltage, populate the corresponding isolated DC/DC converter at `U4`:

| Host Logic Voltage (`VCC`) | Target Microcontrollers | Populated Converter (`U4`) |
|:---|:---|:---|
| **`3.3 V`** | ESP32, ESP8266, RP2040, RP2350, etc. | **Mornsun `B0305S-1WR3`** |
| **`5 V`** | Arduino Uno/Nano/Mega, etc. | **Mornsun `B0505S-1WR3`** |

---

## Interfaces & Pinout

### 1. Host Connector (`J3` – JST-XH 4-Pin)

| Pin | Signal | Direction | Description | Logic Level |
|:---:|:------:|:---------:|:------------|:------------|
| **1** | `GND` | --- | System ground | --- |
| **2** | `VCC` | Power In | Logic supply voltage | 3.3 V or 5 V DC |
| **3** | `TX` | Output | Received DMX data (MCU UART RX) | `VCC` |
| **4** | `RX` | Input | Transmit DMX data (MCU UART TX) | `VCC` |

---

### 2. DMX Connectors (XLR 3-Pin or JST-XH 3-Pin)

#### DMX IN: `J1` (Neutrik NC3MAAH Male XLR) or `J4` (JST-XH 3-Pin)
| Pin | Signal | DMX-512 |
|:---:|:------:|:--------:|
| **1** | `GND_IS` | Pin 1 (Shield) |
| **2** | `DMX_IN-` | Pin 2 (Data -) |
| **3** | `DMX_IN+` | Pin 3 (Data +) |

#### DMX OUT / THRU: `J2` (Neutrik NC3FAAH Female XLR) or `J5` (JST-XH 3-Pin)
| Pin | Signal | DMX-512 |
|:---:|:------:|:--------:|
| **1** | `GND_IS` | Pin 1 (Shield) |
| **2** | `DMX_OUT-` | Pin 2 (Data -) |
| **3** | `DMX_OUT+` | Pin 3 (Data +) |

---

## Jumper Configuration (`JP1` & `JP2`)

The board features two 3-pin solder jumpers (`JP1` for Data-, `JP2` for Data+) to configure the routing for the DMX OUT connector:

```
JP1: [DMX_IN- | DMX_OUT- | DMX_TX-]
JP2: [DMX_IN+ | DMX_OUT+ | DMX_TX+]
```

| Mode | Bridged Pins on JP1 and JP2 | Bridged Nets | Behavior |
|:---|:---:|:---:|:---|
| **DMX THRU** | Pin 1 and Pin 2 | DMX_IN to DMX_OUT | DMX_IN is hardwired directly to DMX_OUT. The host can simultaneously receive the incoming DMX stream. |
| **DMX OUT** | Pin 2 and Pin 3 | DMX_TX to DMX_OUT | DMX_OUT is driven by the second `MAX485E` transceiver on the board. |

---

## Manufacturing and Bill of Materials

The board can be fully populated with readily available components. The smallest SMD components are 0603 which can be soldered manually using hot air or a hot plate. A full BOM with all parts is included alongside the Gerber and Pick-and-Place data in the `production/` folder. The production files are optimized for manufacturing at JLCPCB.

An [interactive HTML BOM](bom/ibom.html) is also available for easy manual component placement.

---

## License & Author

- **Author:** [@tmpeh](https://github.com/tmpeh)
- **Revision:** Rev 1.0
- **Hardware License:** [CERN-OHL-P-2.0](https://spdx.org/licenses/CERN-OHL-P-2.0.html)
