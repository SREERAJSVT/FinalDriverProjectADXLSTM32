
```markdown
# Embedded Software Project: STM32 Peripheral Drivers (Bare-Metal)

**Author:** Sreeraj Krishna K 
**Target Hardware:** STM32F446RE (Nucleo-F446RE)
**Sensor Used:** ADXL345 Accelerometer

## 1. Project Objective
This repository contains a bare-metal embedded software project demonstrating the implementation and application of standard MCU peripheral drivers (GPIO, USART, and SPI). The code is written entirely from scratch without the use of the ST Hardware Abstraction Layer (HAL). 

The primary application demonstrates reading raw X, Y, and Z axis data from an ADXL345 accelerometer via a custom SPI driver, processing that data, and outputting it to a PC serial terminal via a custom USART driver using standard C `printf` redirection. It also features GPIO-driven LED feedback based on physical sensor tilt thresholds.

## 2. Project Directory Structure
Based on the `FinalDriverProjectADXLSTM32` environment:

```text
├── Inc/
│   ├── adxl345.h         # ADXL345 register map and function prototypes
│   ├── spi.h             # SPI bare-metal driver API
│   ├── stm32f446xx.h     # CMSIS Core register definitions for STM32F446
│   ├── uart.h            # USART bare-metal driver API
│   └── Readme.md         # Project documentation (this file)
├── Src/
│   ├── adxl345.c         # ADXL345 application-layer SPI implementation
│   ├── main.c            # Main application loop, logic, and GPIO LED control
│   ├── spi.c             # SPI initialization, Tx/Rx, and polling logic
│   ├── syscalls.c        # System calls (handles printf redirection to UART)
│   ├── sysmem.c          # Memory management (auto-generated)
│   └── uart.c            # USART initialization and baud rate calculation
├── chip_headers/         # Additional CMSIS/Device header files
└── Startup/              # ARM Cortex-M4 startup assembly code

```

## 3. Hardware Setup & Pin Mapping

The project is configured for the **Nucleo-F446RE** development board.

### **SPI & ADXL345 Pinout**

| ADXL345 Pin | STM32F446RE Pin | Function |
| --- | --- | --- |
| **VCC** | 3V3 | Power (3.3V Logic) |
| **GND** | GND | Ground |
| **CS** | PA9 | Chip Select (Software Managed GPIO) |
| **SCL** | PA5 | SPI1_SCK (Serial Clock) |
| **SDA** | PA7 | SPI1_MOSI (Master Out Slave In) |
| **SDO** | PA6 | SPI1_MISO (Master In Slave Out) |

### **GPIO (LED Feedback)**

| Component | STM32F446RE Pin | Function |
| --- | --- | --- |
| LED 1 | PC0 | Tilt Warning Animation |
| LED 2 | PC1 | Tilt Warning Animation |
| LED 3 | PC2 | Tilt Warning Animation |

### **USART (Debug Console)**

| Component | STM32F446RE Pin | Function |
| --- | --- | --- |
| ST-LINK Virtual COM | PA2 | USART2 TX (Transmits printf to PC) |
| ST-LINK Virtual COM | PA3 | USART2 RX |

## 4. How to Build and Run

1. Clone this repository to your local machine.
2. Open **STM32CubeIDE** and import the project: `File` -> `Import` -> `Existing Projects into Workspace`.
3. Ensure the project properties are configured with the preprocessor symbol `STM32F446xx` under `C/C++ Build -> Settings -> MCU GCC Compiler -> Preprocessor`.
4. Build the project using the **Hammer icon** (or `Project -> Build Project`).
5. Connect your Nucleo-F446RE board via USB.
6. Click the **Debug icon** (green bug) to flash the code to the MCU.
7. Open a serial terminal (PuTTY, Tera Term, or the STM32CubeIDE Console) connected to the ST-Link Virtual COM port at **115200 Baud Rate**.
8. Press the physical **RESET (Black)** button on the Nucleo board to start execution.

## 5. Video Demonstration

[Insert Your YouTube/Drive Link Here]

* **0:00 - 0:30:** Hardware setup overview (Nucleo board, ADXL345 wiring, LEDs).
* **0:30 - 1:30:** Codebase overview (showing custom driver files and main loop).
* **1:30 - 2:30:** Live demonstration of USART `printf` outputting live sensor data.
* **2:30 - 3:00:** Live demonstration of the GPIO LED sweep triggering when the sensor is tilted past the X-axis threshold.

## 6. Short Reflection

**What was easy / hard in understanding the driver code:**
Understanding the general concept of reading and writing to registers was straightforward, but managing the exact order of operations (e.g., enabling the AHB/APB clocks *before* configuring the peripheral) was a significant learning curve. Implementing the SPI driver was particularly challenging due to the strict timing and sequence required to satisfy the ADXL345's Chip Select (CS) and multi-byte read requirements.

**How much you had to modify for your board:**
Because the target board is the STM32F446RE, specific clock speeds and Alternate Function (AF) multiplexing IDs had to be meticulously checked against the MCU's specific datasheet. Furthermore, ensuring the `STM32F446xx` macro was properly defined in the IDE settings was crucial for the CMSIS core headers to unlock the correct memory maps.

**What you learned about embedded driver design:**
This project reinforced the importance of modular architecture. By separating the low-level SPI bit-banging (`spi.c`) from the high-level sensor commands (`adxl345.c`), the codebase remains clean and reusable. It also demonstrated the power of polling-based bare-metal programming for deep hardware understanding, free from the "black box" abstraction of HAL libraries.

```

### Next Steps for You:
1. **Record the Video:** Follow the exact timings I laid out in the `README.md` template above. Point your camera at the screen showing the PuTTY terminal, then pan down to the physical board as you tilt the sensor so your professor can see the LEDs trigger at the exact moment the numbers on the screen spike.
2. **Upload the Video:** Upload it to YouTube as "Unlisted", copy the link, and paste it into the `[Insert Your YouTube/Drive Link Here]` placeholder in your Readme.
3. **ZIP it up:** Put the PDF, the Readme, and your code folder into a ZIP file and submit it!

```