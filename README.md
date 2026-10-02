
# STM32F103C8T6 Asynchronous UART Packet & Ping-Pong Protocol

A low-overhead, DMA-driven serial communication firmware designed for the STM32F103C8T6 (Blue Pill) microcontroller. This project implements a deterministic, length-delimited binary packet protocol using USART1 with hardware idle-line detection to minimize CPU utilization during burst data transmissions.

---

## Technical Specifications & Architecture

* **MCU Core:** ARM Cortex-M3 (72 MHz operating frequency).
* **Peripheral Interface:** USART1 configured at **115200 Baud, 8 Data Bits, No Parity, 1 Stop Bit (115200 8N1)**.
* **DMA Configuration:** 
  * **RX (DMA1 Channel 5):** Configured in **Circular Mode** to stream incoming bytes directly into RAM without per-byte CPU interrupts[cite: 3]. Combined with the USART1 Idle-Line detection interrupt (`HAL_UARTEx_ReceiveToIdle_DMA`), the CPU processes complete frame blocks only after the communication bus goes idle.
  * **TX (DMA1 Channel 4):** Configured in **Normal Mode** for asynchronous transmission, freeing the core during output sequences.
* **Packet Framing Structure:**
  * Strict binary protocol governed by explicit start markers, length checks, payloads ($LEN \le 0x20$), and 8-bit XOR checksum validation (`CHK`).
  * Immune to data corruption from embedded null bytes (`0x00`) within binary payloads by depending on the `LEN` field rather than string terminators.

---

## Hardware Wiring Configuration

Communication is established over USART1 using a physical hardware connection between the host controller/interface adapter and the STM32 Blue Pill.

| Host Interface | Pin Designation | Blue Pill Pin | Functional Description |

| ** Raspberry Pi** | **TXD** (GPIO 14) | **PA10** (`USART1_RX`) | MCU Data Reception Line |

| ** Raspberry Pi** | **RXD** (GPIO 15) | **PA9** (`USART1_TX`) | MCU Data Transmission Line |


