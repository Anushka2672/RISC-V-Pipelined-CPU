# 5-Stage Pipelined RISC-V CPU

A 32-bit RV32I RISC-V processor designed using Verilog HDL with
5-stage pipelining, data forwarding, and load-use hazard handling.

## Pipeline Architecture

The processor consists of five stages:

IF → ID → EX → MEM → WB

### 1. Instruction Fetch (IF)
- Program Counter (PC)
- Instruction Memory
- PC + 4 calculation
- IF/ID pipeline register

### 2. Instruction Decode (ID)
- Instruction decoding
- Control Unit
- Register File
- Immediate generation
- ID/EX pipeline register

### 3. Execute (EX)
- ALU operation
- Operand selection
- Data forwarding
- Branch target calculation
- EX/MEM pipeline register

### 4. Memory Access (MEM)
- Load operations
- Store operations
- Data Memory
- MEM/WB pipeline register

### 5. Writeback (WB)
- Selects ALU result or memory data
- Writes result back to Register File

## Hazard Handling

### Data Forwarding

A forwarding unit is implemented to resolve data hazards.

Forwarding signals:

- ForwardAE – controls ALU input A
- ForwardBE – controls ALU input B

Forwarding selections:

- 00 → Register File value
- 01 → Writeback result
- 10 → MEM-stage ALU result

### Load-Use Hazard

A separate hazard detection unit handles load-use hazards.

Signals:

- PCWrite
- IF_ID_Write
- Control_Flush

When a load-use hazard is detected:

- PC is stalled
- IF/ID pipeline register is held
- A bubble is inserted into the ID/EX stage
- The dependent instruction executes after the required data becomes available

## Test Program

The processor was verified using:

    LW  x1, 8(x0)
    SW  x1, 8(x0)
    LW  x2, 4(x0)
    ADD x3, x2, x1

## Verification Results

| Signal | Result |
|---|---|
| x1 | 100 |
| x2 | 0 |
| x3 | 100 |
| Memory[8] | 100 |


RISC-V-Pipelined-CPU/
│
├── RTL/
│   ├── top.v
│   ├── PC.v
│   ├── fetch_cycle.v
│   ├── decoder_cycle.v
│   ├── execute_cycle.v
│   ├── memory_cycle.v
│   ├── writeback_cycle.v
│   │
│   ├── control_unit.v
│   ├── main_decoder.v
│   ├── ALU_decoder.v
│   ├── ALU_Flag.v
│   ├── reg_file.v
│   ├── Sign_Extend.v
│   ├── PC_Adder.v
│   ├── mux.v
│   ├── Mux_3_by_1.v
│   ├── instr_mem.v
│   ├── data_memory.v
│   ├── hazard_unit.v
│   └── hazard_detection_unit.v
│
├── Simulation/
│   └── tb.v
│
├── Waveforms/
│   └── final_waveform.png
│
├── README.md
└── LICENSE
