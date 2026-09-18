# 5-Stage Pipelined RISC-V CPU

A 32-bit RV32I RISC-V processor designed using Verilog HDL with
5-stage pipelining, data forwarding, and load-use hazard handling.

## Pipeline Architecture

The processor consists of five pipeline stages:

**IF → ID → EX → MEM → WB**

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
- Hazard detection

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

---

## Hazard Handling

### Data Forwarding

A forwarding unit is implemented to resolve data hazards and reduce
unnecessary pipeline stalls.

Forwarding signals:

- `ForwardAE` – controls ALU input A
- `ForwardBE` – controls ALU input B

Forwarding selections:

| Value | Source |
|---|---|
| `00` | Register File |
| `01` | Writeback Result |
| `10` | MEM-stage ALU Result |

### Load-Use Hazard

A separate hazard detection unit handles load-use hazards.

Control signals:

- `PCWrite`
- `IF_ID_Write`
- `Control_Flush`

When a load-use hazard is detected:

- PC is stalled
- IF/ID pipeline register is held
- A bubble is inserted into the ID/EX stage
- The dependent instruction executes after the required data becomes available

---

## Test Program

The processor was verified using the following instruction sequence:

```assembly
LW  x1, 8(x0)
SW  x1, 8(x0)
LW  x2, 4(x0)
ADD x3, x2, x1
