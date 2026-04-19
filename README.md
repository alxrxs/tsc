# InkTime -- Smartwatch Open Source cu Display E-Paper
**TSC 2026 -- Andrei-Alexandru Bleortu, 334CA**

---

## Descriere proiect

InkTime este un smartwatch open source cu display e-paper, construit in jurul SoC-ului Nordic nRF52840. Schema electrica a fost furnizata de profesor; sarcina mea a fost sa o transpun in Fusion 360, sa realizez layout-ul PCB si sa-l integrez in carcasa data.

PCB-ul are dimensiunile 36x38mm, grosime 1mm, 4 straturi (TOP, GND, POWER, BOTTOM), cu toate componentele pe layer-ul TOP.

---

## Diagrama bloc

```
                          +--------------------------------------------+
                          |              nRF52840 (U1)                 |
                          |                                            |
  +--------------+  VBUS  |  USB/VBUS                                  |
  |  USB-C (J4)  |------->|                      P0.02 (SCK)  ---------+------------------+
  | KH-TYPE-C-16P|  D+/D- |  USB D+/D-           P0.03 (MOSI) ---------+--------------+   |
  +------+-------+<------>|                      P0.05 (CS)   ---------+----------+   |   |
         |                |  P0.06 (SDA)  ---------------------------- +--+       |   |   |
  +------v-------+        |  P0.07 (SCL)  ---------------------------- +--+ I2C   |   |   |
  |  USBLC6-2SC6Y|        |                      P0.15 (EPD_DC)  ------+--+       |   |SPI|
  |  ESD (D3)    |        |  P0.08 (IMU_INT1)                   P0.16 -+--+       |   |   |
  +------+-------+        |  P1.08 (IMU_INT2)                   P0.17 -+--+       |   |   |
         |                |  P0.11 (PMIC_INT)                          |          |   |   |
  +------v-------+  VREG  |  P0.10 (ALERT)        P1.01 (EPD PWR EN)-->|          |   |   |
  |  BQ25180YBGR |------->|  P0.12 (HAPTIC_EN)                         |          |   |   |
  |  Incarcator  |        |                                            |          |   |   |
  +------+-------+        |  P0.13 (SW1)                               |          |   |   |
         | VBAT           |  P0.14 (SW2)                               |          |   |   |
  +------v-------+        |  P1.02 (SW3)                               |          |   |   |
  |  Baterie     |        |              SWDIO/SWDCLK/SWO/RESET        |          |   |   |
  |  Li-Po       |        |  P0.00/P0.01 (XL1/XL2) <-- X2 32.768kHz    |          |   |   |
  +------+-------+        |  XC1/XC2  <--------------- X1 32MHz        |          |   |   |
         |                |                      ANT  --> Retea RF     |          |   |   |
  +------v-------+        +--------------------------------------------+          |   |   |
  | MAX17048G+T10|<------------------------------- I2C ---------------------------+   |   |
  | Fuel Gauge   |                                                                    |   |
  +--------------+        Magistrala I2C (SDA=P0.06, SCL=P0.07):                      |   |
                          +--------------+  +--------------+  +--------------+        |   |
                          | RT6160AWSC   |  |  BMA421 (IC3)|  | DRV2605YZFR  |        |   |
                          | Buck-Boost   |  |  IMU/Accel.  |  | Haptic (IC2) |        |   |
                          | -> 3V3 (IC9) |  |              |  | -> Motor     |        |   |
                          +--------------+  +--------------+  +--------------+        |   |
                                                                                      |   |
                          Magistrala SPI (SCK=P0.02, MOSI=P0.03):                     |   |
                          +-----------------------------------------------------------+   |
                          |  503480-2400 FPC (J2) -> Display E-Paper                      |
                          |  CS=P0.05, DC=P0.15, RST=P0.16, BUSY=P0.17  <-----------------+
                          +----------------------------------------------

                          Butoane: SW1(P0.13) SW2(P0.14) SW3(P1.02)
                          Debug:   TC2030-IDC (J1) -> SWD + RESET
                          Antena:  2450AT18B100E + retea de adaptare L-C
```

---

## Bill of Materials (BOM)

| Ref | Componenta | Valoare / MPN | Capsula | Cant | JLCPCB | Datasheet |
|-----|------------|---------------|---------|------|--------|-----------|
| U1 | MCU nRF52840 | NRF52840-QIAA-R | AQFN-74 (7x7mm) | 1 | [C190794](https://jlcpcb.com/partdetail/NordicSemiconductor-nRF52840QIAA/C190794) | [Datasheet](https://infocenter.nordicsemi.com/pdf/nRF52840_PS_v1.7.pdf) |
| IC1 | Incarcator baterie | BQ25180YBGR | DSBGA-8 (1.6x1.1mm) | 1 | [C3682423](https://jlcpcb.com/partdetail/C3682423) | [Datasheet](https://www.ti.com/lit/ds/symlink/bq25180.pdf) |
| IC9 | Regulator Buck-Boost DC-DC | RT6160AWSC | WLCSP-15 (2.3x1.4mm) | 1 | [C7065276](https://jlcpcb.com/partdetail/C7065276) | [Datasheet](https://www.richtek.com/assets/product_file/RT6160A/DS6160A-00.pdf) |
| IC3 | IMU / Accelerometru | BMA421 | LGA-12 (2x2mm) | 1 | [C5242966](https://jlcpcb.com/partdetail/BoschSensortec-BMA421/C5242966) | [Datasheet](https://files.pine64.org/doc/datasheet/pinetime/BST-BMA421-FL000.pdf) |
| IC2 | Driver motor haptic | DRV2605YZFR | DSBGA-9 | 1 | [C81079](https://jlcpcb.com/partdetail/C81079) | [Datasheet](https://www.ti.com/lit/ds/symlink/drv2605.pdf) |
| U2 | Fuel gauge Li-Po | MAX17048G+T10 | WLP-8 (0.9x1.8mm) | 1 | [C2682616](https://jlcpcb.com/partdetail/C2682616) | [Datasheet](https://www.analog.com/media/en/technical-documentation/data-sheets/max17048-max17049.pdf) |
| J4 | Conector USB-C | KH-TYPE-C-16P | SMD 16P | 1 | [C709357](https://jlcpcb.com/partdetail/C709357) | [Datasheet](https://datasheet.lcsc.com/lcsc/2108252330_Korean-Hroparts-Elec-TYPE-C-31-M-12_C165948.pdf) |
| J2 | Conector FPC (e-Paper) | 503480-2400 | SMD 24P, pas 0.5mm | 1 | [C122434](https://jlcpcb.com/partdetail/C122434) | [Datasheet](https://www.molex.com/content/dam/molex/molex-dot-com/products/automated/en-us/salesdrawingpdf/503/5034/503480-2400_sd.pdf) |
| J1 | Conector debug | TC2030-IDC | Tag-Connect 6P | 1 | N/A (footprint) | [Datasheet](https://www.tag-connect.com/wp-content/uploads/bsk-pdf-manager/TC2030-IDC-NL_Datasheet_8.pdf) |
| ANT1 | Antena chip 2.4GHz | 2450AT18B100E | SMD 1.6x0.8mm | 1 | [C2917717](https://jlcpcb.com/partdetail/C2917717) | [Datasheet](https://www.johansontechnology.com/datasheets/2450AT18B100E/2450AT18B100E.pdf) |
| D3 | Protectie ESD USB | USBLC6-2SC6Y | SOT-363 | 1 | [C2969755](https://jlcpcb.com/partdetail/C2969755) | [Datasheet](https://www.st.com/resource/en/datasheet/usblc6-2.pdf) |
| D2, D4, D5 | Dioda Schottky | MBR0530 | SOD-123 | 3 | [C232832](https://jlcpcb.com/partdetail/C232832) | [Datasheet](https://www.onsemi.com/pdf/datasheet/mbr0530t1-d.pdf) |
| Q1 | N-MOSFET (driver EPD) | SI1308EDL-T1-GE3 | SC-70-3 | 1 | [C469327](https://jlcpcb.com/partdetail/C469327) | [Datasheet](https://www.vishay.com/docs/66783/si1308edl.pdf) |
| Q2 | P-MOSFET (alimentare EPD) | DMG2305UX-7 | SOT-323 | 1 | [C150470](https://jlcpcb.com/partdetail/C150470) | [Datasheet](https://www.diodes.com/assets/Datasheets/DMG2305UX.pdf) |
| SW1, SW2, SW3 | Buton tactil | EVP-AKE31A | SMD | 3 | [C569760](https://jlcpcb.com/partdetail/C569760) | [Datasheet](https://industrial.panasonic.com/cdbs/www-data/pdf/ATV0000/ATV0000CE15.pdf) |
| X1 | Crystal HF | 32MHz | SMD 3225 | 1 | [C113671](https://jlcpcb.com/partdetail/C113671) | -- |
| X2 | Crystal LF (RTC) | 32.768kHz | SMD | 1 | [C145140](https://jlcpcb.com/partdetail/C145140) | -- |
| L7 | Inductor (DC-DC) | FTC252012SR47MBCA / 0.47uH | 0402 | 1 | [C5832368](https://jlcpcb.com/partdetail/C5832368) | [Datasheet](https://product.tdk.com/system/files/dam/doc/product/inductor/inductor/smd/catalog/inductor_commercial_power_mlp2016_en.pdf) |
| L1 | Inductor adaptare antena | 3.9nH | 0402 | 1 | -- | -- |
| L2 | Inductor decuplare RF | 10uH | 0402 | 1 | -- | -- |
| L3 | Inductor decuplare RF | 15nH | 0402 | 1 | -- | -- |
| L5 | Inductor (boost EPD) | 68uH | 0402 | 1 | -- | -- |
| C1, C2 | Condensator sarcina crystal HF | 12pF | 0201 | 2 | [C426109](https://jlcpcb.com/partdetail/C426109) | -- |
| C17, C18 | Condensator sarcina crystal LF | 12pF | 0201 | 2 | [C426109](https://jlcpcb.com/partdetail/C426109) | -- |
| C3, C4 | Condensator adaptare antena | 1pF | 0201 | 2 | [C185624](https://jlcpcb.com/partdetail/C185624) | -- |
| C8, C12, C7, C5 | Condensator decuplare | 100nF | 0201 | 4 | [C160831](https://jlcpcb.com/partdetail/C160831) | -- |
| C6, C14, C20, C21 | Condensator bulk | 4.7uF | 0402 | 4 | [C23687](https://jlcpcb.com/partdetail/C23687) | -- |
| C24, C39 | Condensator bulk (VREG) | 10uF | 0402 | 2 | [C105884](https://jlcpcb.com/partdetail/C105884) | -- |
| C25, C33 | Condensator iesire (3V3) | 22uF | 0402 | 2 | [C919306](https://jlcpcb.com/partdetail/C919306) | -- |
| C37, C38 | Condensator filtru PMIC | 1uF | 0402 | 2 | [C268023](https://jlcpcb.com/partdetail/C268023) | -- |
| R17, R18 | Rezistenta pull-up I2C | 3.3kohm | 0201 | 2 | [C274874](https://jlcpcb.com/partdetail/C274874) | -- |
| R5, R7, R8 | Rezistenta pull-up butoane | 10kohm | 0201 | 3 | [C106225](https://jlcpcb.com/partdetail/C106225) | -- |
| R9, R2_EP_DR, R_PWR_EPD | Pull-up / configurare | 10kohm | 0201 | 3 | [C106225](https://jlcpcb.com/partdetail/C106225) | -- |
| R1_USB, R2_USB | Rezistente CC USB-C | 5.1kohm | 0201 | 2 | [C274341](https://jlcpcb.com/partdetail/C274341) | -- |
| R1, R2, R4 | Jumper zero-ohm | 0ohm | 0201 | 3 | [C106227](https://jlcpcb.com/partdetail/C106227) | -- |
| R1_EP_DR | Rezistenta limitare curent EPD | 0.47ohm | 0201 | 1 | -- | -- |
| R_TYPE_SEL | Selectie tip display | 2.2ohm | 0201 | 1 | -- | -- |
| EPD_C1-C12 | Condensatoare HV EPD | 1uF/50V | 0402 | 9 | -- | -- |
| C29-C32 | Condensator debounce butoane | 1uF | 0402 | 4 | [C268023](https://jlcpcb.com/partdetail/C268023) | -- |

---

## Descriere hardware

### Componentele principale

**nRF52840 (U1)** -- MCU central. Cortex-M4F la 64MHz, 1MB flash, 256KB RAM, radio 2.4GHz integrat (BLE 5.0 + IEEE 802.15.4). Capsula AQFN-74, 7x7mm, 74 de pad-uri.

**BQ25180YBGR (IC1)** -- incarcator Li-Po single-cell de la Texas Instruments. Gestioneaza power path-ul USB-C, curentul de incarcare configurabil prin I2C (pana la 350mA), si semnalizeaza evenimentele de stare prin pinul INT (PMIC_INT -> P0.11). Adresa I2C: 0x6A.

**RT6160AWSC (IC9)** -- regulator buck-boost de la Richtek, WLCSP-15. Converteste tensiunea variabila a bateriei (3.0-4.2V) in 3.3V stabil pentru restul circuitului. Controlabil prin I2C. Adresa I2C: 0x75 (7-bit, conform datasheet sectiunea 16.23).

**BMA423 (IC3)** -- accelerometru triaxial Bosch, LGA-12 (2x2mm). Are numarator de pasi si detectie de gesturi integrate in hardware. Comunica prin I2C, adresa 0x18 (pinul CSB legat la 3V3, SDO legat la GND prin R1). Doua linii de intrerupere: INT1 -> P0.08, INT2 -> P1.08.

**DRV2605YZFR (IC2)** -- driver motor haptic de la Texas Instruments, DSBGA-9. Controleaza un motor ERM sau LRA, cu biblioteca de tipare de vibratie integrata. I2C, adresa 0x5A. Activat prin HAPTIC_EN (P0.12).

**MAX17048G+T10 (U2)** -- fuel gauge Li-Po de la Maxim/ADI, WLP-8. Estimeaza starea de incarcare a bateriei prin algoritmul ModelGauge (fara rezistenta de sunt). I2C, adresa 0x36. Semnalul !ALERT merge la P0.10.

---

### Arhitectura de alimentare

```
USB-C (5V VBUS)
     |
     v
BQ25180 (IC1) -- incarcare baterie + power path
     | SYS -> sina VREG
     v
RT6160 (IC9) -- buck-boost
     | VOUT = 3.3V -> sina 3V3
     v
nRF52840, BMA423, DRV2605, MAX17048
```

Alimentarea display-ului e-paper este separata: Q2 (P-MOSFET DMG2305UX-7) comuteaza sina EPD_3V3, controlat de P1.01. Tensiunile de gate driver (PREVGH/PREVGL, +/-20V) sunt generate de un circuit boost cu L5 (68uH), Q1 (N-MOSFET SI1308EDL) si trei diode Schottky MBR0530.

---

### Pini nRF52840 utilizati

| GPIO | Functie | Conectat la |
|------|---------|-------------|
| P0.00 / XL1 | Crystal LF XL1 | X2 (32.768kHz) |
| P0.01 / XL2 | Crystal LF XL2 | X2 (32.768kHz) |
| P0.02 | SPI SCK | J2 pin 13 (display) |
| P0.03 | SPI MOSI | J2 pin 14 (display) |
| P0.05 | EPD CS | J2 pin 12 |
| P0.06 | I2C SDA | IC1, IC9, IC3, U2, IC2 |
| P0.07 | I2C SCL | IC1, IC9, IC3, U2, IC2 |
| P0.08 | IMU INT1 | IC3 (BMA423) |
| P0.10 / NFC2 | ALERT (fuel gauge) | U2 (MAX17048) |
| P0.11 | PMIC_INT | IC1 (BQ25180) |
| P0.12 | HAPTIC_EN | IC2 (DRV2605) |
| P0.13 | SW1 | Buton + pull-up 10kohm |
| P0.14 | SW2 | Buton + pull-up 10kohm |
| P0.15 | EPD DC | J2 pin 11 |
| P0.16 | EPD RST | J2 pin 10 |
| P0.17 | EPD BUSY | J2 pin 9 |
| P0.18 / RESET | RESET | TC2030 J1, TP_RESET |
| P1.00 | SWO | TC2030 J1, TP_SWO |
| P1.01 | EPD power enable | Gate Q2 |
| P1.02 | SW3 | Buton + pull-up 10kohm |
| P1.08 | IMU INT2 | IC3 (BMA423) |
| SWDIO | SWD Data | TC2030 J1, TP_SWDIO |
| SWDCLK | SWD Clock | TC2030 J1, TP_SWDCLK |
| VBUS | Detectare VBUS | BQ25180 IN + J4 |
| D+ / D- | USB Full-Speed | USBLC6-2SC6Y (D3) |
| XC1, XC2 | Crystal HF | X1 (32MHz) |

Pinii P0.04, P0.09, P0.19-P0.31, P1.03-P1.07, P1.09-P1.15 nu sunt utilizati in schema.

---

### Interfete de comunicatie

**I2C** (SDA=P0.06, SCL=P0.07) -- magistrala partajata cu 5 dispozitive:
- BQ25180 (0x6A) -- incarcator
- RT6160 (0x75) -- regulator DC-DC
- BMA423 (0x18) -- IMU
- MAX17048 (0x36) -- fuel gauge
- DRV2605 (0x5A) -- driver haptic

Pull-up-uri de 3.3kohm (R17, R18) pe ambele linii. R4 si R2 sunt rezistente de 0ohm care conecteaza IC9 (RT6160) la magistrala -- pot fi inlaturate pentru izolare la nevoie.

**SPI** (SCK=P0.02, MOSI=P0.03, CS=P0.05) -- exclusiv pentru display-ul e-paper prin conectorul FPC J2 (Molex 503480-2400, 24 pini, pas 0.5mm). Semnale suplimentare: DC (P0.15), RST (P0.16), BUSY (P0.17).

**USB Full-Speed** -- D+/D- merg prin USBLC6-2SC6Y (D3) pentru protectie ESD, apoi la pinii dedicati ai nRF52840. Rezistentele CC de 5.1kohm (R1_USB, R2_USB) identifica dispozitivul ca UFP pe USB-C.

**SWD** -- SWDIO, SWDCLK, SWO (P1.00), RESET (P0.18) disponibili prin conectorul Tag-Connect TC2030-IDC (J1) si pad-urile de test corespunzatoare.

---

### Ceas si RF

**Cristale:** X1 (32MHz) pe pinii XC1/XC2 -- necesar pentru radio BLE. X2 (32.768kHz) pe P0.00/P0.01 (XL1/XL2) -- pentru RTC. Condensatori de sarcina de 12pF pe ambele cristale.

**Antena:** 2450AT18B100E (Johanson Technology), chip antenna SMD 1.6x0.8mm. Conectata prin retea de adaptare Pi: L1 (3.9nH) serie si C3/C4 (1pF) shunt. C22 montat N.C. (rezervat pentru acordaj). Antena este plasata la marginea PCB-ului, cu decupaj in placa sub ea -- fara cupru si fara trasee in acea zona.

---

### Butoane

Trei butoane tactile Panasonic EVP-AKE31A, active-low, cu pull-up de 10kohm la 3V3 si condensator de debounce de 1uF pe fiecare linie.

| Buton | GPIO | Pull-up | Debounce |
|-------|------|---------|----------|
| SW1 | P0.13 | R5 | C30 |
| SW2 | P0.14 | R8 | C31 |
| SW3 | P1.02 | R7 | C29 |

---

### Debug

TC2030-IDC (J1) -- footprint Tag-Connect, fara conector fizic pe placa. Pinout:

| Pin | Semnal |
|-----|--------|
| 1 | 3V3 |
| 2 | SWDIO |
| 3 | GND |
| 4 | SWDCLK |
| 5 | GND |
| 6 | RESET |

Pad-uri de test disponibile in silkscreen: TP_SWDIO, TP_SWDCLK, TP_SWO, TP_RESET, TP_3.3V, TP_GND, TP_VBAT, TP_BAT_GND, TP_3V3, TP_VREG, TP_SCL, TP_SDA, TP_OP, TP_ON.

---

## Estimari consum

Calcule estimative bazate pe datasheeturile componentelor.

| Mod | Contribuitori | Curent estimat |
|-----|---------------|----------------|
| Deep sleep (doar RTC) | nRF52840 system-on: ~2.6uA, BMA423 low-power: ~3.5uA, MAX17048: ~3uA, RT6160 quiescent: ~60uA, DRV2605 standby: ~40uA | **~110uA** |
| BLE advertising (1s interval) | nRF52840 medie advertising: ~0.5mA + ~110uA sistem | **~0.6mA** |
| Activ (BLE conectat + refresh display) | nRF52840: ~4mA, varf refresh: ~26mA (cateva secunde), IMU: ~150uA | **~5-30mA** |
| Incarcare USB | BQ25180 la 200mA | **~200mA** |

Cu o baterie de 400mAh si un tipar de utilizare de ~5 minute activ + 55 minute standby pe ora, curentul mediu iese in jur de 0.5-0.6mA, ceea ce teoretic da cateva saptamani de autonomie. In practica, cu stack-ul BLE si refresh-uri regulate de display, realitatea va fi probabil undeva la 5-7 zile.

---

## Note PCB

Schema electrica a fost furnizata de dl. Tudose. Munca mea a constat in transpunerea ei in Fusion 360 si realizarea layout-ului PCB conform specificatiilor primite.

**Constrangeri respectate:**
- PCB 4 straturi: TOP (componente + rutare), GND (plan de masa dedicat), POWER (plan de alimentare), BOTTOM (rutare)
- Toate componentele plasate pe layer-ul TOP
- Trasee de putere (3V3, VBUS, VREG) cu latime 0.3mm; semnale de date minimum 0.15mm
- Condensatoarele de decuplare de 100nF plasate cat mai aproape de pinii de alimentare ai IC-urilor
- Fara cupru si fara trasee sub antena; decupaj PCB inclus sub 2450AT18B100E
- Fara unghiuri de 90deg in trasee

**DRC:** rulat cu fisierul de reguli furnizat. Au ramas erori de tip "Dimension" la cele trei butoane si mufa USB (acceptate conform instructiunilor de laborator) si avertismentele ERC "Only INPUT pins on NET" (de asemenea acceptate).

**Observatie privind BMA421 vs BMA423:** in schema deviceset-ul este BMA423, dar valoarea componentei scrie BMA421. Cele doua sunt pin-compatibile (acelasi footprint LGA-12, 2x2mm), deci nu afecteaza PCB-ul.

---

## Structura repository-ului

```
+-- Hardware/
|   +-- Schematic.sch          -- Schema electrica (Fusion 360 / Eagle)
|   +-- Board.brd              -- Layout PCB (Fusion 360 / Eagle)
|   +-- Schematic.pdf          -- Printout schema
+-- Manufacturing/
|   +-- gerbers.zip            -- Fisiere Gerber pentru fabricare
|   +-- InkTime.bom            -- Bill of Materials (CSV)
|   +-- InkTime.cpl            -- Pick and Place (CSV)
+-- Mechanical/
|   +-- InkTime_complete.step  -- Vedere 3D explodata (STEP)
|   +-- InkTime_complete.f3d   -- Asamblare Fusion 360
+-- Images/
|   +-- *.png                  -- Randari PCB si 3D
+-- LICENSE                    -- Apache 2.0
+-- README.md                  -- Acest fisier
```

---

## Licenta

GNU Affero General Public License. Vezi [LICENSE](LICENSE).
