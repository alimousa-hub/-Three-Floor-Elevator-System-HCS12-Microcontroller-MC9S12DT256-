# 🛗 Three-Floor Elevator System — HCS12 Microcontroller (MC9S12DT256)

A fully functional **3-floor elevator simulation** built in **HCS12 Assembly language** on the Motorola MC9S12DT256 microcontroller. The system integrates push buttons, an LCD display, LED indicators, a PWM buzzer, and an analog weight sensor into one complete embedded system that behaves like a real elevator.

> 📚 *COE 324 – Microprocessors Lab | Lebanese American University*
>
> *Ali Mousa · Hussein Itani · Hassan Hamad · Abbas El Toufeili*

---

## 📁 Files

| File | Description |
|---|---|
| `elevator_system.asm` | Full HCS12 assembly source code |
| `Final_Project_Report.docx` | Complete project report with explanation and analysis |

---

## ⚙️ System Overview

The elevator reads requests from **6 push buttons** (3 inside the cabin + 3 outside on each floor). A modulus counter interrupt (MCCNT) regularly checks button states and updates the elevator direction. The system displays real-time status on an LCD, blinks the correct LED for direction, and beeps on every action.

---

## 🧰 Hardware Components

| Component | Function |
|---|---|
| MC9S12DT256 (PBMCUSLK board) | Main microcontroller |
| 16×2 LCD Display (HD44780, SPI) | Shows floor number and movement status |
| Green LED (PTT7) | Elevator going **up** |
| Yellow LED (PTT6) | Elevator going **down** |
| Red LED (PTT5) | Doors open / elevator stopped |
| PWM Buzzer (Channel 4) | Beeps on arrival and overweight warning |
| ATD Weight Sensor (AN0) | Detects overload — prevents unsafe movement |
| 6 Push Buttons (PORTA) | Floor requests (inside + outside cabin) |

---

## 🖥️ LCD Display Messages

| Message | When it appears |
|---|---|
| `F0` | Elevator is at Ground Floor |
| `F1` | Elevator is at Floor 1 |
| `F2` | Elevator is at Floor 2 |
| `GOING UP` | Elevator is moving upward |
| `GOING DOWN` | Elevator is moving downward |

---

## 💡 LED & Buzzer Behavior

| Indicator | Behavior | Meaning |
|---|---|---|
| 🟢 Green LED | Blinking | Moving up |
| 🟡 Yellow LED | Blinking | Moving down |
| 🔴 Red LED | Blinking | Doors open / stopped at floor |
| 🔔 Buzzer (short beep) | On arrival | Floor reached |
| 🔔 Buzzer (continuous) | Overweight detected | Too much load — doors stay open |

---

## 🗺️ State Machine Logic

The system uses a 3-bit `STATE` variable updated by the MCCNT interrupt every ~100ms by reading PORTA:

```
Bit 0 → Ground Floor button
Bit 1 → Floor 1 button
Bit 2 → Floor 2 button
```

### Floor Decision Logic:

```
At GND  → if STATE > 1  : go UP
At F1   → if STATE > 2  : go UP to F2
          if STATE < 2  : go DOWN to GND
          if STATE = 6  : special case (both F0 & F2 pressed) → go UP first
          if STATE = 3  : special case (both F0 & F2 pressed) → go DOWN first
At F2   → if STATE < 4  : go DOWN
```

---

## 🔌 Pin Configuration

| Pin | Direction | Function |
|---|---|---|
| PORTA (PA7–PA2) | Input | Push buttons (floor requests) |
| PTT7 | Output | Green LED (going up) |
| PTT6 | Output | Yellow LED (going down) |
| PTT5 | Output | Red LED (stopped/doors open) |
| PWM Channel 4 | Output | Buzzer |
| ATD0 AN0 | Analog Input | Weight sensor |
| SPI0 (Port M) | Output | LCD communication |

---

## 📋 Key Subroutines

| Subroutine | Description |
|---|---|
| `MYISR` | MCCNT interrupt: reads buttons, updates STATE |
| `GOINGUP` / `GOINGDOWN` | Writes movement message to LCD |
| `FLOOR0/1/2` | Displays current floor on LCD |
| `BLINKGREEN` / `BLINKYELLOW` / `STOP1` | Controls LED blinking |
| `BEEP` | Short arrival beep via PWM |
| `BEEP1` | Weight check — continuous alarm if overloaded |
| `SENDINST` / `SENDDATA` | Sends commands/characters to LCD via SPI |
| `DELAY` / `DELAY1` / `DELAY2` | Timed delays (~100ms, ~300ms, ~1.5s) |

---

## 🚀 How to Run

1. Open the project in **Freescale CodeWarrior for HCS12**
2. Connect the **PBMCUSLK board** via USB/BDM interface
3. Build the project (Absolute Assembly)
4. Flash to the MC9S12DT256
5. Power the board — the LCD will initialize and the system starts at Ground Floor

**Required tools:**
- Freescale CodeWarrior IDE (HC12)
- PBMCUSLK development board
- MC9S12DT256 microcontroller

---

## 👨‍💻 Author

**Ali Mousa** — Mechatronics Engineering @ Lebanese American University 🇱🇧
