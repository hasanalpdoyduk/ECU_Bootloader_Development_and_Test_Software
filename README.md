
#  ECU Bootloader Development & Test Software

*[2024–2025] Electrical & Electronics Engineering — Senior Project*

![Platform](https://img.shields.io/badge/MCU-dsPIC33-blue?style=for-the-badge&logo=microchip)
![Interface](https://img.shields.io/badge/Interface-CAN%20%7C%20UART-green?style=for-the-badge)
![Language](https://img.shields.io/badge/Language-C%20%7C%20Python-yellow?style=for-the-badge&logo=python)
![IDE](https://img.shields.io/badge/IDE-MPLAB%20X-red?style=for-the-badge)
![PCB](https://img.shields.io/badge/PCB-KiCad-purple?style=for-the-badge)

A custom bootloader was developed for an ECU platform, enabling firmware updates without the need for external programmers. The system was designed around a 16-bit dsPIC33 microcontroller and supports reliable firmware transfer over both CAN and UART communication interfaces.

A dedicated custom PCB was designed and manufactured, including the dsPIC33 MCU, CAN transceiver, input protection circuitry, and LDO-based power architecture. During PCB development, controlled-impedance CAN differential pair routing was implemented with consideration for return current paths, cross-interference isolation, and signal integrity principles.

The bootloader software uses a block-based firmware transfer architecture to ensure reliable memory programming and data integrity during interrupt-driven operation. Firmware packets received through CAN or UART are validated and securely written into the microcontroller flash memory.

To improve usability and streamline testing, a Python-based GUI application was developed for real-time firmware upload, monitoring, and control. In addition, a separate test application with a graphical interface was implemented to dynamically configure PWM parameters for ECU functional validation.

The complete system, including bootloader operation and PWM control functionality, was successfully tested and validated.

## System Architecture

```text
+------------------------+
|   PC Update Software   |
+------------------------+
          |
      CAN / UART
          |
+------------------------+
|      Bootloader        |
|   Flash Programming    |
|   CRC Verification     |
|  Jump to Application   |
+------------------------+
          |
+------------------------+
|    Main Application    |
+------------------------+
```

## Tools & Libraries

- KiCad: Used for schematic and PCB design.

- MATLAB Simulink Device Blocks for Microchip: Used to model and generate initial versions of embedded code.

- MPLAB X IDE: Used to compile and flash generated C code to the dsPIC33 MCU.

- Python (PyQt): Used for GUI development to support user interaction and test control.

## Hardware

### Main Components

To initially load the bootloader onto the microcontroller or test the PWM control code, an external debugger is required. In this project, Microchip’s **PICkit 5** is used to program the dsPIC33 device.

| Type         | Component                                                        |
| ----------------- | ------------------------------------------------------------------ |
| Microcontroller | [dsPIC33](https://ww1.microchip.com/downloads/aemDocuments/documents/OTH/ProductDocuments/DataSheets/dsPIC33EVXXXGM00X-10X-Family-Data-Sheet-DS70005144H.pdf) |
| CAN Transceiver | [MCP2561](https://ww1.microchip.com/downloads/en/DeviceDoc/20005167C.pdf) |
| LDO              | [LF50](https://www.st.com/resource/en/datasheet/lfxx.pdf) |
| TVS Diode| [SMAJ36CA](https://www.vishay.com/docs/88390/smaj50a.pdf) |
| Schottky Diode| [SS8PH10HM3](https://www.vishay.com/docs/88989/ss8ph10.pdf) |

All hardware components are listed in the [Bill of Materials (BOM)](https://github.com/hasanalpdoyduk/Test-Software-and-Bootloader-Development-for-an-ECU/blob/main/KiCAD/ECU_SW_Control/Production_Files/BOM/ECU_SW_Control.csv).

### Hardware Block Diagram

![Block Diagram](https://github.com/hasanalpdoyduk/Test-Software-and-Bootloader-Development-for-an-ECU/blob/main/Images/block_diagram_last.png)

### 3D View of PCB

This PCB is manufactured and tested successfully.

![3D](https://github.com/hasanalpdoyduk/ECU_Bootloader_Development_and_Test_Software/blob/main/Images/3D_view.png?raw=true)

## Software

### Repository Structure
```text
 ECU_Bootloader_Development_and_Test_Software

  Codes/                                  # All embedded firmware & GUI source code
     v0/                                 # Initial prototype (digital output test)
       digital_output_2_0.c
       digital_output_2_0_main.c
   
     v1/                                 # First bootloader + app iteration
        Bootloader_22.05.25_empty.X/    # MPLAB X — Bootloader project (no verification)
          main.c
          mcc_generated_files/
              boot/                      # Boot core: process, image, header, UART adaptor
              memory/                    # Flash driver (flash.h / flash.s)
              ...                        # Clock, UART, interrupt, system drivers
      
        App_22.05.25_RB4HIGH.X/         # MPLAB X — Application project (RB4 HIGH test)
          main.c
          mcc_generated_files/
              boot/                      # App-side boot header & interrupt tables
              ...                        # Clock, pin manager, system drivers
      
        UART_PWM-both.X/                # MPLAB X — Combined UART + PWM app
          main.c
          autoload.py                    # Helper script for auto-loading hex
          mcc_generated_files/
      
        UART_PWM-both_GUI/              # Python GUI v1
           UART_PWM-both_GUI.py
   
     v2/                                 # Second iteration — GUI extended
        Bootloader_22.05.25_empty.X/    # Same bootloader, refined config
        App_22.05.25_RB4HIGH.X/         # App with UART driver added
        UART_PWM-both.X/                # Updated combined firmware
        double_b_bootlaoder_checker/    # Python GUI v2 (dual bootloader checker)
           UART_PWM-both_GUI2222.py
   
     v3/                                 # Final version — CRC32 verification added
         Bootloader_24.05.25_checkCRC.X/ # MPLAB X — Bootloader with CRC32 verify
           main.c
           mcc_generated_files/
               boot/                      # boot_verify_crc32.c/.h, crc32_asm.S
               ...
         App_22.05.25_RB4HIGH.X/         # Final application project
         UART_PWM-both.X/                # Final combined UART + PWM firmware
         double_b_bootlaoder_checker/    # Final Python GUI
            UART_PWM-both_GUI2222.py

  UBHA/                                   # Microchip Unified Bootloader Host Application
     UnifiedHost-1.19.3/                 # UBHA v1.19.3 (Java)
       UnifiedHost-1.19.3.jar             # Main executable JAR
       lib/                               # Serial, CAN, HID communication libraries
       logging.properties
     javafx-sdk-17.0.15/                 # JavaFX runtime dependency
        lib/

  KiCAD/                                  # Hardware design files
    ECU_SW_Control.zip                     # Full project archive
     ECU_SW_Control/
         ECU_SW_Control/                 # Main KiCad project
           ECU_SW_Control.kicad_pcb       # PCB layout
           ECU_SW_Control.kicad_sch       # Top-level schematic
           CAN.kicad_sch                  # CAN subsystem schematic
           MCU.kicad_sch                  # MCU subsystem schematic
           LDO.kicad_sch                  # Power regulation schematic
           Connector.kicad_sch            # Connector schematic
           Voltage_Protection.kicad_sch   # TVS / protection schematic
           ECU_SW_Control_lib.*           # Custom symbol & footprint library
         External_lib/                   # Imported component libraries
           LIB_CX3225CA12000D0KPSC1/      # Crystal oscillator library
           LIB_LF50/                      # LDO regulator footprint
           LIB_V10P10HM3_A_H/             # Schottky diode library
         Production_Files/               # Manufacturing outputs
             BOM/
               ECU_SW_Control.csv         # Bill of Materials
             Gerbers/                    # Gerber files (GTL, GBL, masks, silkscreen…)
             Drills/                     # Drill files (PTH / NPTH)
             PickNPlace/                 # Pick & place position files

  Datasheets/                             # Component reference documents
    dsPIC33_DATASHEET.pdf
    dsPIC33_UART.pdf
    lfxx.pdf                               # LF50 LDO datasheet
    uart.pdf
    LDO Thermal Considerations.pdf

  Images/                                 # Visuals, test results, screenshots
    block_diagram_last.png                 # System block diagram
    3D_view.pdf                            # 3D render of the PCB
    board.jpg                              # Assembled PCB photo
    basic_arch.png                         # Basic architecture diagram
    bootloader_register_map.png            # Memory map diagram
    UBHA_*.png                             # UBHA GUI screenshots (select, flash, verify…)
    UART_GUI*.png                          # Python GUI screenshots
     OscillatorSs/                       # Oscilloscope captures (ADS*.BMP)
    *.pdf                                  # Signal & layout measurement exports

  Layout_Problems/                        # PCB revision notes & findings
    ECU_LED_Findings.pptx.pdf
    ECU_SW_Control_Findings.pptx.pdf

  Report_Presentation/                    # Academic deliverables
    EE402_Final_Report_(...).pdf           # Full project report
    EE402_Final_Presentation_(...).pptx   # Defense presentation

 README.md
```

### Embedded Firmware (C / MPLAB X)

- **Bootloader**: Resides in a reserved memory region; handles host commands, erases flash sectors, and writes incoming firmware blocks.
- **Application (PWM Control)**: A demonstration application that runs after the bootloader hands off execution — validates the complete update flow end-to-end.
- **Communication Drivers**: Supports both **CAN** (via MCP2561) and **UART** for host-to-device data transfer.

### Host GUI — UBHA (Python / PyQt)

The **UBHA** (Universal Bootloader Host Application) provides:
- Firmware file (`.hex`) selection and parsing
- Real-time flash progress tracking
- CAN / UART interface selection and configuration
- Bootloader status feedback and error reporting

## Usage

### Prerequisites

- MPLAB X IDE (v6.x or later) with XC16 compiler
- Python 3.8+ with PyQt5
- PICkit 5 (for initial bootloader flashing)
- CAN or UART interface hardware

### Steps

1. Flash the Bootloader using PICkit 5 and MPLAB X with MCC:
   ```
   Open Codes/v3/Bootloader → Build → Program Device
   ```

2. Connect the ECU

Connect the board to the PC through either:
- CAN interface
- UART interface


3. Run the Host GUI:

Run the Python-based GUI software for firmware upload and monitoring.
   ```bash
   cd UBHA
   pip install -r requirements.txt
   python main.py
   ```

3. Select Interface (CAN / UART) in the GUI and choose your firmware `.hex` file.

4. Click Flash — the bootloader will erase, program, and verify the new firmware automatically.

The bootloader:
- Receives firmware packets
- Verifies data integrity
- Programs flash memory
- Jumps to the main application

## Supporting Sources 

All technical details and explanations related to the project are available in the [project report](https://github.com/hasanalpdoyduk/Test-Software-and-Bootloader-Development-for-an-ECU/blob/main/Report_Presentation/EE402_Final_Report_AlpDoyduk_S025015_I%CC%87smailAkbas_S024094.pdf). For more in-depth insights or specific inquiries, feel free to reach out via GitHub or refer to the resources listed below.

- [MCC dsPIC33 Bootlader - Video Guide](https://www.youtube.com/watch?v=2LhW11LbNhY): A step-by-step YouTube tutorial demonstrating how to implement a bootloader for dsPIC33 using Microchip Code Configurator (MCC).

- [Microchip Bootloader Library for 16-bit Devices](https://www.microchip.com/en-us/software-library/dspic33-pic24-bootloader): Official documentation and downloadable libraries for implementing bootloaders on 16-bit dsPIC33/PIC24 microcontrollers.

- [16-bit Bootloader Using MCC - Online Course](https://mu.microchip.com/16-bit-bootloaders-using-mcc-device-side): A Microchip University course explaining how to develop a bootloader for dsPIC33 using MCC, covering both theory and practice.

## Authors

- [Hasan Alp Doyduk](https://www.github.com/hasanalpdoyduk)
- [Ismail Akbaş](https://www.github.com/ismailakbas)

Supervised by: Asst. Prof. Umut Başaran

## License

This project was developed as an academic senior design project. Please contact the authors for usage or collaboration inquiries.

