# SPI Master-Slave Verification Project

A complete SystemVerilog verification environment for a Serial Peripheral Interface (SPI) communication system with master and slave modules.

## Project Overview

This project implements a full SPI communication protocol with a comprehensive testbench infrastructure for verification. The design includes an SPI master that generates a serial clock and transmits 12-bit data to an SPI slave module, which receives and reconstructs the data.

## Features

- **12-bit SPI Communication**: Configurable data width with LSB-first transmission
- **Clock Generation**: Master generates SPI clock (sclk) from system clock
- **Full Verification Environment**: Complete UVM-style testbench with layered architecture
- **Transaction-Level Modeling**: Reusable transaction class for data handling
- **Constrained Random Verification**: Random stimulus generation for thorough testing
- **Scoreboard Checking**: Automated comparison between sent and received data

## Design Modules

![ddef9e78-4b5a-4c52-b97e-e5ed95d98283](https://github.com/user-attachments/assets/66137320-1680-4a68-859b-c0165153a5d0)

### SPI Master (`spi_master`)
- Generates serial clock (sclk) with configurable frequency divider
- Implements state machine: IDLE → SEND → IDLE
- Transmits 12-bit data LSB-first
- Controls chip select (cs) and MOSI signals

### SPI Slave (`spi_slave`)
- Detects transmission start via chip select
- Receives serial data on MOSI line
- Reconstructs 12-bit parallel data
- Signals completion via done flag

### Top Module (`top`)
- Integrates SPI master and slave
- Provides system-level connectivity

## Testbench Architecture

<img width="1024" height="559" alt="image_1ba5c318-c27b-442d-8b83-2696d26bc076" src="https://github.com/user-attachments/assets/865fdcb8-a0e7-4154-afd5-104e59de5a52" />


### Verification Components

1. **Transaction Class**: Encapsulates data and control signals
2. **Generator**: Creates randomized transactions
3. **Driver**: Applies transactions to DUT inputs
4. **Monitor**: Observes and captures DUT outputs
5. **Scoreboard**: Compares driver and monitor data for verification
6. **Environment**: Orchestrates all verification components

## File Structure

```
├── Design/
│   └── spi.sv            # SPI master, slave, and top module
├── SV_Testbench/
│   └── tb.sv             # Testbench top module with all classes
└── README.md             # This file
```

## Getting Started

### Prerequisites

- Xilinx Vivado Design Suite
- Basic understanding of SPI protocol
- Familiarity with SystemVerilog verification concepts

### Running the Simulation

1. **Open Vivado and create a new project**

2. **Add design sources:**
   - Add `Design/spi.sv` as design source

3. **Add simulation sources:**
   - Add `SV_Testbench/tb.sv` as simulation source

4. **Run Behavioral Simulation:**
   - Click on "Run Simulation" → "Run Behavioral Simulation"
   - Or use TCL console: `launch_simulation`

5. **View waveforms:**
   - Waveforms will automatically open in Vivado's simulator
   - Add signals as needed for debugging

### Customizing Test Cases

Modify the transaction count in the testbench:

```systemverilog
initial begin
  env = new(vif);
  env.gen.count = 5;  // Change number of transactions
  env.run();
end
```

## Simulation Output

The testbench produces detailed logs for each transaction:

```
[DRV] : RESET DONE
-----------------------------------------
[GEN] : din : 2847
[DRV] : DATA SENT TO DAC : 2847
[MON] : DATA SENT : 2847
[SCO] : DRV : 2847 MON : 2847
[SCO] : DATA MATCHED
-----------------------------------------
```

![d28d1d7b-5f09-4ffd-91a7-6b3699590815](https://github.com/user-attachments/assets/87b3f44f-b9dc-4546-b825-0319c14efe52)


## Key Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| Data Width | 12 bits | Configurable in design |
| Clock Divider | 10 | System clock to SPI clock ratio |
| Transmission Mode | LSB First | Bit ordering |
| Clock Polarity | CPOL=0 | Idle low |
| Clock Phase | CPHA=0 | Sample on rising edge |

## Timing Characteristics

- **SCLK Frequency**: System clock / 22 (countc < 10, toggle)
- **Setup/Hold Time**: Controlled by state machine timing
- **CS to SCLK Delay**: 1 SCLK cycle

## Design States

### Master States
- **IDLE**: Waiting for new data request
- **SEND**: Transmitting data bits serially
- **COMP**: Completion (transitions to IDLE)

### Slave States
- **DETECT_START**: Waiting for CS assertion
- **READ_DATA**: Receiving serial data

## Known Limitations

- Fixed 12-bit data width (requires RTL modification to change)
- Single slave support (no multi-slave addressing)
- No error detection/correction mechanisms
- LSB-first transmission only

## Waveform
![4eec200f-b415-4315-abb8-b77f375cca56](https://github.com/user-attachments/assets/322225b7-8221-47ea-87af-c769455c709a)


