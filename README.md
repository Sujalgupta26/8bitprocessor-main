# ⚡ IITSoC'23 Electronics Project

## Overview

This project consists of three main components:

1. **Datapath**
2. **Control Unit**
3. **Testbenches**

---

### 1. Datapath

The Datapath executes all the instructions in the instruction set. It is designed at the register-transfer level and includes the following components:

#### 1.1 Input Multiplexer

A 4-to-1 multiplexer selects one of four different inputs to be written into the accumulator:

1. **Immediate Value (`imm_dp`)**: Used for the LDI instruction.
2. **User Input (`input_dp`)**: Used for the IN instruction.
3. **Register File Content**: Used for the LDA instruction.
4. **ALU and Shifter Result**: Used by arithmetic and logical instructions.

#### 1.2 Conditional Flags

Two conditional flags, **zero** and **positive**, are set by comparators:

- **Zero Flag**: Set using an 8-input NOR gate.
- **Positive Flag**: Set by checking the most significant sign bit.

#### 1.3 Accumulator

The accumulator is an 8-bit wide register with `write` and `clear` control signals:

- **Write (`accwr_dp`)**: Writes values into the accumulator.
- **Clear (`rst_dp`)**: Clears the accumulator on reset.

The accumulator output is sent to three destinations:

1. **Output Buffer**: For the OUT instruction.
2. **ALU Operand (A)**: For arithmetic operations.
3. **Register File**: For the STA instruction.

---
#### 1.4 Register File

The register file consists of eight 8-bit wide locations. It has the following features:

- **Addressing**: Three address lines (`rfaddr_dp2`, `rfaddr_dp1`, `rfaddr_dp0`) are used to address the eight locations for both reading and writing.
- **Read Port**: Always active, providing the value from the currently selected address location.
- **Write Port**: Controlled by the `rfwr_dp` line, which must be asserted to write a value to the currently selected address location.

The register file does not require separate read and write address lines, as all instructions either perform a read or a write, but not both simultaneously.

#### 1.5 ALU (Arithmetic Logic Unit)

The ALU supports eight operations, selected by three select lines (`alusel_dp2`, `alusel_dp1`, `alusel_dp0`):

1. **PASS**
2. **AND**
3. **OR**
4. **NOT**
5. **ADDITION**
6. **SUBTRACTION**
7. **INCREMENT**
8. **DECREMENT**

#### 1.6 Shifter

The Shifter supports four operations, selected by two select lines (`shiftsel_dp1`, `shiftsel_dp0`):

1. **SHIFT RIGHT**
2. **SHIFT LEFT**
3. **NO CHANGE**
4. **ROTATE RIGHT**

#### 1.7 Output Buffer

The output buffer is an 8-bit register with an enable control signal (`outen_dp`). When the enable line is asserted, the value from the accumulator is stored in the buffer and used as the output for the system. The enable line is controlled by either the `OUT A` instruction or the system reset signal.

---

### 2. Control Unit

The Control Unit is responsible for managing the control signals sent to the Datapath. It operates as a finite-state machine (FSM), cycling through three main steps:

1. **Fetch**: Fetches the instruction from memory.
2. **Decode**: Decodes the fetched instruction.
3. **Execute**: Executes the instruction by asserting the appropriate control signals.

#### 2.1 Fetching an Instruction

During the fetch step, the Control Unit:

- Sets up a memory address on the address bus.
- Instructs the external memory to output the instruction from the specified memory location onto the data bus.
- Reads the instruction from the data bus.

#### 2.2 Decoding the Instruction

In the decode step:

- The Control Unit extracts the opcode bits from the fetched instruction.
- It determines the type of instruction and jumps to the corresponding state in the FSM to execute the instruction.

#### 2.3 Executing the Instruction

During the execute step:

- The Control Unit asserts the appropriate control signals to the Datapath or external devices.
- Depending on the instruction, it may involve operations like arithmetic, logic, memory access, or control flow changes.

---
#### 2.4 Instruction Details

##### Two-Operand Instructions

For instructions that require two operands:

- The **accumulator (A)** is always used as one operand.
- The second operand may be a register, specified by the last three bits of the instruction encoding.

**Examples:**

- **LDA (Load Accumulator from Register)**: Loads the accumulator with the content of the specified register.
- **ADD (Add)**: Adds the content of the accumulator with the specified register and stores the result in the accumulator.
- **LDI (Load Immediate)**: Loads the accumulator with an immediate value specified in the instruction.

##### One-Operand Instructions

One-operand instructions use the accumulator as both the operand and the destination:

- The last four bits in the encoding are used to further decode the specific instruction.

**Example:**

- **INC (Increment Accumulator)**: Increments the value in the accumulator.

##### Instructions Using a Memory Address

For instructions involving a memory address:

- An additional six bits are needed to access the 64 bytes of memory.
- These bits are specified in the second byte of the instruction.

**Example:**

- **LDM (Load Accumulator from Memory)**: Loads data from a memory location specified in the second byte into the accumulator.

##### Jump Instructions

For jump instructions:

- The last four bits of the encoding differentiate between absolute and relative jumps.
- If the last four bits are zeros, it is an absolute jump. Otherwise, they represent a relative displacement from the current program counter (PC) location.

---
## 🧪 Testbenches

Testbenches are used to verify the functionality of the Datapath and Control Unit. They simulate various scenarios and validate that the system behaves as expected under different conditions.

### Key Aspects of the Testbenches

- **Functional Testing**: Ensures that all components of the Datapath and Control Unit perform their intended functions correctly.
- **Edge Cases**: Tests include edge cases to verify that the system handles unusual or extreme conditions properly.
- **Performance Testing**: Assesses the performance of the system under different loads and conditions.
- **Coverage**: Ensures that all parts of the design are exercised by the testbenches, providing high test coverage.

### Example Testbenches

- **Basic Operation Tests**: Validate basic operations like load, add, and store.
- **Boundary Condition Tests**: Check behavior at the limits of data sizes and ranges.
- **Stress Tests**: Measure the system's ability to handle high-frequency instructions and data.

---





---
