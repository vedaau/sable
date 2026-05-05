---
title: Module Bill of Materials
tags:
- tag1
- tag2
---

## Overview
This page lists every component used to build one Sable Audio Subsystem board, along with the quantity, manufacturer part number, supplier, unit cost, and total cost for each line item. The list is divided into the major active components at the top (the microcontroller, the microphone, and the voltage regulator), the supporting passives in the middle (resistors, capacitors, the inductor, the diode, and the fuse), and the connectors and headers at the bottom. Every part on this list matches the parts shown on the Schematic page and placed on the PCB page, and the totals at the bottom give the full cost to build one board. This bill of materials was used to place the actual order for the parts that populate the finalized board.


## Bill of Materials 

*Table #1: Audio Subsystem BOM table*

| **Part Name/Description** | **Qty** | **Unit Cost** | **Total Cost** | **Manufacture** | **Manufacturer #** | **Vendor Link** |**Datasheet Link** | **Schematic Reference Designators** |
|:--------------------|:----|:---------------|:-----|:--------|:-----|:-----|:----|:-----|
0.1 µF Ceramic Capacitor, +/-10%, X7R, 50V, 0805 package | 5 | $0.10 | $0.10 | KEMET | C0805F104K5RACTU | PRLTA 109 | n/a | C2-C6 |
LED (red), SMD | 1 | $0.10 | $0.10 | Lite-On Inc. | LTST-C170GKT | [DigiKey](https://www.digikey.com/en/products/detail/liteon/LTST-C170GKT/269226) | [datasheet link](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/895/LTST-C170GKT.pdf) | D1 |
Fuse, 2A  | 1 | n/a | n/a | n/a | n/a | PRLTA 109 | n/a | F1 |
Barrel Jack,  2X5.5MM | 1 | $0.95 | $0.95 | Same Sky | PJ-002AH-SMT-1 | [DigiKey](https://www.digikey.com/en/products/detail/same-sky-formerly-cui-devices/PJ-002AH-SMT-1/408453) | [datasheet link](https://mm.digikey.com/Volume0/opasdata/d220001/medias/docus/1172/PJ-002AH-SMT.pdf) | J1 |
I2S Microphone MEMS | 1 | $3.27 | $3.27 | TDK InvenSense | ICS-43434 | [DigiKey](https://www.digikey.com/en/products/detail/tdk-invensense/ICS-43434/6140298) | [datasheet link](https://invensense.tdk.com/en-us/search-result?query=ics-43434%20datasheet) | MK1 |
Variety Value Resistors | 10 | $0.10 | $1.00 | YAGEO | n/a | PRLTA 109 | n/a | R1-R8 |
Diode 40 V 1A Surface Mount SOD-123F | 1 | $2.44 | $2.44 | Diodes Incorporated | 1N5819HW1-7-F | [DigiKey](https://www.digikey.com/en/products/detail/diodes-incorporated/1N5819HW1-7-F/5768523) | [datasheet link](https://www.diodes.com/assets/Datasheets/1N5819HW1.pdf) | D2 |
Test Points, 0.090" | 10 | $0.35 | $3.50 | Keystone Electronics | 5029 | [DigiKey](https://www.digikey.com/en/products/detail/keystone-electronics/5029/3904815?_gl=1*gjgmtv*_up*MQ..*_gs*Nw..&gclid=ea56b6deca171f7c7a39df75a6f8b415&gclsrc=3p.ds) | [datasheet link](https://www.keyelco.com/userAssets/file/K75p61.pdf) | TP1-TP10 |
Extra Pin Headers | 1 | $1.64 | $1.64 | CNC Tech | 220-1-16-003 | [DigiKey](https://www.digikey.com/en/products/detail/cnc-tech/220-1-16-003/3441516) | [datasheet link](https://www.cnctech.us/pdfs/220-1-XX-00X_.pdf) | U1 |
3.3V Regulator | 1 | $0.60 | $0.60 | TAEJIN | LM2575GR-3.3 | [DigiKey](https://www.digikey.com/en/products/detail/taejin/LM2575GR-3-3/22237505) | [datasheet link](https://www.htckorea.co.kr/Datasheet/Step-down/LM2575.pdf) | U3 |
PIC18F27Q43-I/SS Microcontroller | 1 | $1.65 | $1.65 | Microchip Technology | PIC18F27Q43-I/SS | [DigiKey](https://www.digikey.com/en/products/detail/microchip-technology/PIC18F27Q43-I-SS/11588228) | [datasheet link](https://ww1.microchip.com/downloads/en/DeviceDoc/PIC18F27-47-57Q43-Data-Sheet-DS40002147D.pdf) | U6 |
2x4 IDC Connectors | 2 | $1.48 | $1.48 | Molex | 0702460801 | PRLTA 109 | [datasheet link](https://www.molex.com/en-us/products/part-detail/702460801?display=pdf) | U4, U5 |
