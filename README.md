# nrf52-at-cmds-quectel-lib

This library implements a hardware-agnostic interface to control the **Quectel EG915U-LA** cellular module using AT commands over a **Nordic nRF52840 MCU**. The design follows an object-oriented paradigm in C and a layered architecture, enabling modularity, portability, and unit testability.

## Project Overview

The project provides a structured way to interface with cellular modems, specifically focusing on the Quectel EG915U-LA. It handles everything from low-level UART communication to high-level MQTT protocol management and network status monitoring.

### Key Architectural Layers

*   **Hardware Abstraction Layer (HAL) (`/src/core/hw/`)**: Contains low-level drivers for the nRF52840 peripherals, including UARTE, GPIO, Power management, and SPI/Flash.
*   **Firmware (FW) Layer (`/src/core/fw/`)**:
    *   **`quectel_at_cmds`**: High-level module abstracting AT commands into C functions, including a full MQTT client implementation.
    *   **`helper_at_cmds`**: Manages "send-and-wait" logic, pattern matching (e.g., "OK", "+QMTCONN"), and timeouts.
    *   **`sim_module`**: Manages cellular lifecycle (booting, network registration, signal quality).
    *   **`tools`**: Provides utility queues (circular buffers) for asynchronous data handling.
*   **Application Layer (`/src/core/app/`)**: Coordinates the SIM module and application logic, utilizing FreeRTOS tasks to manage the modem state machine.

## Design Patterns

*   **Object-Oriented C**: Modules use a "Class" pattern where a `struct` contains both data attributes and a function table (vtable), initialized via `Create_Obj` functions.
*   **Layered Decoupling**: High-level application logic is decoupled from hardware specifics through multiple abstraction layers.

## Technologies Used

*   **RTOS**: FreeRTOS for concurrency management.
*   **SDK**: Nordic nRF5 SDK.
*   **Communication**: AT Commands over UART.
*   **Toolchains**: Supports SEGGER Embedded Studio (SES) and arm-none-eabi-gcc.

## Getting Started

A minimal example of initializing the library and sending an AT command can be found in `quectel_at_cmds/main.c`.

The main API definitions for cellular and MQTT operations are located in `quectel_at_cmds.h`.

---

## Sequence Diagram: Quectel SIM Test Execution Flow

This diagram illustrates the initialization and task execution flow of the `main_app_quectel_sim_test.c` example.

Diagram Summary:
*    **Initialization:** Shows main() setting up the SIM module and Quectel shared data before spawning the FreeRTOS tasks.
*    **QUECTEL_MAIN Task:** Illustrates the periodic execution of the modem state machine (handling connections and MQTT).
*    **QUECTEL_PUB Task:** Shows the data publishing loop that sends payloads to the MQTT broker.
*    **SIM_Task:** Displays the module processing logic, which only executes once a broker connection is established.

### OPEN IMAGE: [Seq_Diagram_Quectel_Comm](https://github.com/fhernando-m5/nrf52-at-cmds-quectel-lib/blob/main/imgs/Seq_Diagram_Quectel_Comm.svg?raw=1)

![Imagen de Seq_Diagram_Quectel_Comm](imgs/Seq_Diagram_Quectel_Comm.svg?raw=1 "Seq_Diagram_Quectel_Comm")
