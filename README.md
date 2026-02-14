# FPGA Combination Lock & Keypad Controller

## 📌 Overview
This project implements a synchronous, FSM-based digital combination lock and matrix keypad controller on the Intel DE10-Lite FPGA. Written entirely in SystemVerilog, the design handles raw hardware inputs from a 4x4 matrix keypad, featuring robust mitigation for mechanical switch bounce and signal metastability]. 

## ✨ Key Features
* **Matrix Keypad Scanning:** A custom 20-state Finite State Machine (FSM) actively sweeps the rows of a 4x4 keypad and reads the column responses to detect specific key presses in real-time.
* **Hardware Debouncing & Synchronization:** Utilizes dual flip-flop synchronizers to safely cross clock domains and prevent metastability from asynchronous human button presses. A dedicated debounce timer ensures clean, reliable single-pulse outputs for every physical key press.
* **Dynamic Password Management:** Users can input, store, and validate 6-digit passwords/attempts (stored as 24-bit logic vectors).
* **Multi-State FSM Architecture:**  Controls the main system flow (Set Password, Attempt Password, Locked, Open).
  * Manages digit tracking to accurately route sequential keypad inputs into the correct memory registers.
* **Real-Time Visual Feedback:** Translates active states and stored key codes directly to the DE10-Lite's 7-segment displays (`OPEN` / `LOCKED` messages) and LED arrays.

## ⚙️ Technical Implementation & Module Breakdown
* **Language:** SystemVerilog (RTL Design)
* **Hardware:** Intel DE10-Lite FPGA (MAX 10), 4x4 Matrix Keypad, Arduino GPIO Header.
* **Clocking:** Driven by the onboard 50MHz clock (`MAX10_CLK1_50`), with custom tick counters to manage state transitions and debounce delays.

### Core Modules
* `combo` (Top-Level): Orchestrates the data path between the keypad inputs, the digit-tracking FSM (`safe_digits`), the main access FSM (`combo_fsm`), and the 7-segment display drivers. Stores the active 24-bit `password` and `attempt` registers.
* `kb_db` (Debouncer & Synchronizer): Hardened input logic that passes raw row/col wires through double-flopped synchronizers (`row_F2`, `col_F2`). Includes a delay counter to filter out mechanical bouncing.
* `keypad` (Scanner): Maps directly to the `ARDUINO_IO` pins to interface with the physical keypad. Drives the row signals low sequentially while sampling the columns to uniquely identify all 16 keys (0-9, A-D, *, #).
* `sevenseg` (Decoder): Combinational logic block that maps 4-bit hexadecimal data to 8-bit visual patterns for the board's 7-segment displays.

## 🚀 Usage
1. Flash the compiled bitstream to the DE10-Lite board.
2. Connect the 4x4 matrix keypad to the designated `ARDUINO_IO` pins (Pins 3-10).
3. Use the onboard pushbuttons (`KEY`) to trigger system resets and state changes.
4. Input digits via the keypad to set a new 6-digit combination or attempt an unlock. The system will display `OPEN` or `LOCKED` on the 7-segment displays based on validation.
