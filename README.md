# Cycle_accurate_simulator
Repository for the project of the course Computer Architecture, Monsoon 2022
# Cycle-accurate simulator of a 5-stage RISC CPU
Instructions implemented:
1. AND - AND operation
2. OR - Bitwise OR operation
3. ADD - Addition
4. SUB - Subtraction
5. ADDI - Add Immediate
6. BEQ - Branch equal
7. LW - Load Word
8. SW - Store Word
9. SLL - Shift Logical Left
10. SRA - Shift Right Arithmetic

**Peripheral Support**

In addition to the above instructions, two special instructions are implemented that
are not present in the standard RISC-V ISA. These instructions are:

**1. LOADNOC:**

Syntax: _LOADNOC <rs2> <rs1> <imm>_

This instruction will store the data in the register rs2 to special
memory-mapped registers whose address will be (rs1+imm). In
theory, this instruction is very similar to the “Store” instruction
of RISC-V. However, a regular store would write data to the
Data Cache while this instruction will write the data to
memory-mapped registers. Note that the registers in the
register file are not the same as memory-mapped registers.
Memory Mapped Registers are not present in the register file.
In standard RISC-V, Memory Mapped Registers are written by
the same load/store instructions that are used to write to the
data cache. Whether to actually write to the data cache or to
the memory-mapped registers is determined by the address of
the load store. For the sake of simplicity, we assume
that addresses in the range 0x0000 - 0x3fff will be
loading/storing from the data cache and addresses from
0x4000 will be loading/storing from memory-mapped registers.
For the purpose of this project, there are only 4 memory
mapped registers of 32 bits. Let us call these registers _MMR0_,
_MMR1_, _MMR2_, _MMR3_, and the mapping is:



| Register    | Address |
|---------|-----|
| MMR0    | 0x4000  |
| MMR1   | 0x4004  |
| MMR2    | 0x4008  |
| MMR3   |  0x400c  |

Eg. to store a 32-bit integer 0x33293745 into MMR2; we
would first need to move this integer and 0x4000 into a
Register File Registers(let’s say R1 and R2) respectively, then
use the instruction LOADNOC R1, R2, #8.

**2. SENDNOC:**

Syntax: _STORENOC_

This instruction is similar to LOADNOC, but it will always write
the integer 1 to the Memory Mapped Register with the address
0x4010(MMR4). Note that this instruction does not take any
argument. So we need to hardcode that when this instruction
is issued, the memory-mapped register with address 0x4010
will be written with the value 1.

For clarity, you can refer to the memory map given below:


| Region    | Start Address | End Address    |
|---------|-----|-------------|
| D-Cache | 0x0000  | 0x3fff  |
| MMR0    | 0x4000  | 0x4003   |
| MMR1   | 0x4004  | 0x4007 |
| MMR2    | 0x4008  | 0x400b |
| MMR3    | 0x400c  | 0x400f |
| MMR4    | 0x4010  | 0x4013 |

For simplicity, you can assume that any address beyond 0x4014 will
never be accessed.

# Implementation details

You can create the following modules/classes in the project:

**1. CPU class:**

(Possible Inputs:- clock, data_read) 

(Possible outputs:- data_write,addr_read,addr_write)

**Possible inputs and outputs definitions**

_Clock_:- Global clock for the system

_Data_read_:- is the data sent from the cache to the CPU

_Data_write_:- is the data sent out to the cache from the CPU

_Addr_read_:- the address from which the CPU needs to read the data

_Addr_write_:- the address on which the CPU needs to write the data

It contains the logic for the pipeline stages and the register file
that would be used by the pipeline stages.

The pipeline stage and RegisterFile can also be modeled as a class.



**2. Data Memory class:**

(Possible Inputs:- clock, addr,data_write) 

(Possible outputs:- data)

_Clock_:- Global clock for the system

_Data_write_:- is the data sent out to the cache from the CPU

_Addr_:- the address on which data is read or written to

_isRead_:- is the data written to memory or sent to the CPU

It simulates the data memory element. The memory element returns the response to
the given request from the CPU(read/write) after x(delay) clock cycles the request
was submitted. When memory is instantiated, a user should be able to set the
value of the delay.



**3. Instruction Memory class:**

(Possible Inputs:- clock, addr) 

(Possible outputs:- data)

_Clock_:- Global clock for the system

_Addr_:- the address from which instruction is read

_Data_:- the instruction data which is sent to the CPU

It simulates the data memory element. The memory element returns the response to
the given request from the CPU(read) after x(delay) clock cycles the request was
submitted. When memory is instantiated, the user should be able to set the
value of delay.
