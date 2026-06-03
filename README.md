# Design-and-Implementation-of-32-bit-Multi-stage-RISC-V-Processor
# Design-and-Implementation-of-32-bit-Multi-stage-RISC-V-Processor
-------------------------------------------------

<details>
<summary><b>1. Why Choose a Multi-Stage Processor Over a Single-Cycle Processor?</b> </summary>
<br>
  
**i. Single Cycle Processor**

- Before diving into the multi-stage pipeline processor, let's first understand the singlecycle processor. Then we can see why pipelining is important.
<img width="747" height="244" alt="Image" src="https://github.com/user-attachments/assets/b6fbe355-b7ff-4f13-a39d-e4f6676bc349" />

-  A Single Cycle RISC-V Processor is a basic CPU design in which every
instruction is executed in exactly one clock cycle.
- This includes all five stages of instruction execution: instruction fetch, decode,
execute, memory access, and write-back.



- Program:

```
main:
addi x1, x0, 5
addi x2, x0, 10
add x3, x2, x1
```

- **Demo Video**

[![Watch the video](https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_)

- **Processor Pipeline Execution Example**

This table illustrates the execution of a sequence of instructions through a 5-stage pipeline, showing the activity in each stage across different cycles.

| Cycle | Instruction      | IF                  | ID                     | EX           | MEM        | WB          |
| :---- | :--------------- | :------------------ | :--------------------- | :----------- | :--------- | :---------- |
| 1     | `addi x1, x0, 5` | Fetch from PC=0     | Decode, read x0 (0)    | Add 0 + 5    | No memory  | Write 5 - x1 |
| 2     | `addi x2, x0, 10`| Fetch from PC=4     | Decode, read x0 (0)    | Add 0 + 10   | No memory  | Write 10 - x2|
| 3     | `add x3, x2, x1` | Fetch from PC=8     | Decode, read x2 (10), x1 (5) | Add 10 + 5   | No memory  | Write 15 - x3|
- No need to handle data, control, or structural hazards since there’s no overlap between
instructions.
- The clock cycle has to be long enough to finish the slowest instruction so faster
instructions waste time.
- Only one instruction runs at a time, so it’s slow overall.


</details>



-----------------------
<details>
<summary><b>2. Stages in Multi stage pipelined processor</b></summary>

<br> 

  ## Stage pipeline:
IF → ID → EX → MEM → WB




### IF – Instruction Fetch:
- Here we fetch an instruction from memory.
- PC register already contains the address of next instruction, so simply whatever is there
in PC from that memory location we read.
### ID – Instruction Decode:
- Here we try to decode the opcode and find out the what kind of instruction it is.
- While decoding is going on it also do some fetching.
- Assuming that there will be 16bit immediate data, it will be taking that last 16bit of
instruction and it will be doing a sign extension to 32bits.
### EX-Execute:
- Here we execute the instruction or some instructions we have to compute the effective
address.
- It’s actual memory address from which data will be loaded (LW) or to which data will
be stored(SW).
### MEM – Memory Access:
- In this stage here it actually d memory access, read & write from memory.
- For branch instruction it decides whether to branch or not.
### WB – Write Back:
 The result of an instruction is written back to the register file.
- After an instruction finishes calculating, we store the result into register in the register
file. 

- Let us understand the pipeline stages in a multi-stage processor by taking an example. 
  **Program:**

```
text
main:
addi x1, x0, 5
addi x2, x0, 10
nop
nop
add x3, x2, x1 
```

- **Demo Video**

[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1kfnzHK05PBeWIAu1yKHBBrIyfgy1o7B-%20/view?usp=drive_link))



- **Pipelined Execution**

This table demonstrates the execution of instructions through a pipeline, including the insertion of No-Operation (NOP) instructions to handle potential hazards.

| Cycle | IF                | ID                | EX                | MEM               | WB                |
| :---- | :---------------- | :---------------- | :---------------- | :---------------- | :---------------- |
| 1     | `addi x1, x0, 5`  |                   |                   |                   |                   |
| 2     | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |                   |                   |
| 3     | NOP               | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |                   |
| 4     | NOP               | NOP               | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |
| 5     | `add x3, x2, x1`  | NOP               | NOP               | `addi x2, x0, 10` | `addi x1, x0, 5`  |
| 6     |                   | `add x3, x2, x1`  | NOP               | NOP               | `addi x2, x0, 10` |
| 7     |                   |                   | `add x3, x2, x1`  | NOP               | NOP               |
| 8     |                   |                   |                   | `add x3, x2, x1`  | NOP               |
| 9     |                   |                   |                   |                   | `add x3, x2, x1`  |
</details>




---------------------------------------------------



<details>
<summary><b></b> 3. Micro-Operations in Each Pipeline Stage</summary> 
 <br>
<img width="1792" height="576" alt="Image" src="https://github.com/user-attachments/assets/794d6bbf-c1d0-4362-8a00-00b34bcb7b13" />
A breakdown of the different stages in the pipeline.



### **IF/ID Stage**

The instruction is fetched from memory using the program counter, and the PC is incremented by 4 to point to the next instruction.



### **ID/EX Stage**

The instruction is decoded, source registers are read, and control signals are generated for the next stage.



### **EX/MEM Stage**

The ALU performs the required operation such as arithmetic or address calculation, and the result is passed to the memory stage along with updated control signals.



### **MEM/WB Stage**

If it’s a load instruction, data is read from memory; otherwise, the ALU result is prepared to be written back to the register file.

</details>



---------------------------------------------------



<details>
<summary><b></b>4. Types of Hazards in a Multi-Stage Pipeline </summary> 

<br> 


## Data Hazards:
When an instruction depends on the result of a previous instruction that hasn’t yet
completed.

Example:
```
addi x1, x0, 5 # x1 = 5
addi x2, x0, 10 # x2 = 10
add x3, x1, x2 # x3 = x1 + x2 → data hazard here 

```

**Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1hl8igFd6qln0DeALkVckzusGewKk0ejF/view?usp=drive_link))

- **Pipelined Execution (No Stalls)**

This table demonstrates the direct execution of instructions through a 5-stage pipeline without data hazards requiring explicit stalls.

| Cycle | IF                | ID                | EX                | MEM               | WB                |
| :---- | :---------------- | :---------------- | :---------------- | :---------------- | :---------------- |
| 1     | `addi x1, x0, 5`  |                   |                   |                   |                   |
| 2     | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |                   |                   |
| 3     | `add x3, x1, x2`  | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |                   |
| 4     |                   | `add x3, x1, x2`  | `addi x2, x0, 10` | `addi x1, x0, 5`  |                   |
| 5     |                   |                   | `add x3, x1, x2`  | `addi x2, x0, 10` | `addi x1, x0, 5`  |
| 6     |                   |                   |                   | `add x3, x1, x2`  | `addi x2, x0, 10` |
| 7     |                   |                   |                   |                   | `add x3, x1, x2`  |

- add x3, x1, x2 is trying to read x1 and x2 in its ID stage.
- But x1 and x2 haven’t reached WB yet, so their correct values aren't available yet.
- This is a Read After Write (RAW) data hazard.

<br>

## Control Hazards:
Hazards caused by branch or jump instructions that change the program counter
(PC). 
- Example:
  
 - What's is hazard in this
- Assume x1 = 5, x2 = 5 initially
```
addi x1, x0, 5 # x1 = 5
addi x2, x0, 5 # x2 = 5
beq x1, x2, target # If equal, jump to target
addi x3, x0, 10 # This should be skipped if branch is taken
addi x4, x0, 20 # This will be target
target:
addi x5, x0, 30 # This is where we land if beq taken 
```

- **Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1IAJcRL9DWJ0aPErHSCn9yp1pkTqZmGHF/view?usp=drive_link))

- **Pipelined Execution with Branch Instruction**

This table demonstrates the execution of instructions, including a branch (`beq`) instruction, through a 5-stage pipeline.

| Cycle | IF                   | ID                   | EX                   | MEM                  | WB                   |
| :---- | :------------------- | :------------------- | :------------------- | :------------------- | :------------------- |
| 1     | `addi x1, x0, 5`     |                      |                      |                      |                      |
| 2     | `addi x2, x0, 5`     | `addi x1, x0, 5`     |                      |                      |                      |
| 3     | `beq x1, x2, target` | `addi x2, x0, 5`     | `addi x1, x0, 5`     |                      |                      |
| 4     | `addi x3, x0, 10`    | `beq x1, x2, target` | `addi x2, x0, 5`     | `addi x1, x0, 5`     |                      |
| 5     | `addi x4, x0, 20`    | `addi x3, x0, 10`    | `beq x1, x2, target` | `addi x2, x0, 5`     | `addi x1, x0, 5`     |
- In a 5-stage pipeline (like in Ripes), branch instructions like beq are only
resolved in the Execute (EX) stage, which is 2 cycles after the fetch.
- The branch decision (beq) is only made in the Execute (EX) stage.
- Meanwhile, the next instructions (addi x3, addi x4) are already fetched and
possibly entered decode or execute stages.
- This creates a Control Hazard — the CPU is unsure whether to continue with
x3/x4 or jump to target.

<br>

## Structural Hazard:
A Structural Hazard occurs when hardware resources are not sufficient to support
multiple instructions executing in parallel in the pipeline. 

- Example:

```
lw x1, 0(x2) # Instruction 1 — Load word from memory into x1
addi x3, x0, 5 # Instruction 2 — Set x3 = 5 (uses ALU, no memory access)
sw x4, 0(x5) # Instruction 3 — Store word from x4 into memory at address in x5 

```

- **Demo Video**
[![Watch the video]([https://img.youtube.com/vi/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/0.jpg)](https://drive.google.com/file/d/19tvVzC2Peg3M1gEwgkp9zjb7W3Y67ugn/view?usp=drive_](https://drive.google.com/file/d/1V3A_KQz1bCuY_dOkxBQSieeDTePHF8T/view?usp=drive_link))
- lw x1, 0(x2) and sw x4, 0(x5) involve memory access.
- If memory is not properly initialized or x2/x5 don't point to valid memory,
these memory-related instructions don't actually read or write correctly.
- But addi x3, x0, 5 is a pure ALU instruction (doesn't depend on memory),
so it always works and updates x3.

</details>


--------------------------------------------------



<details>
<summary><b>5. Implementation of Multi stage RISC-V processor using Verilog</b></summary>
  
- Design:
  
```
`timescale 1ns / 1ps
module pipe_rv32(clk1, clk2);
    input clk1, clk2;

    // Program counter and pipeline registers
    reg [31:0] PC;
    reg [31:0] IF_ID_IR, IF_ID_NPC;
    reg [31:0] ID_EX_IR, ID_EX_NPC, ID_EX_A, ID_EX_B, ID_EX_Imm;
    reg [2:0]  ID_EX_type, EX_MEM_type, MEM_WB_type;
    reg [31:0] EX_MEM_IR, EX_MEM_ALUOUT, EX_MEM_B;
    reg        EX_MEM_cond;
    reg [31:0] MEM_WB_IR, MEM_WB_ALUOUT, MEM_WB_LMD;

    reg [31:0] RegFile[0:31];
    reg [31:0] Mem[0:1023];

    reg HALTED;
    reg TAKEN_BRANCH;

    // Opcodes (7-bit fields)
    parameter OP_R     = 7'b0110011; // R-type
    parameter OP_I     = 7'b0010011; // ALU imm (ADDI, SLTI)
    parameter OP_LOAD  = 7'b0000011; // LW
    parameter OP_STORE = 7'b0100011; // SW
    parameter OP_BRANCH= 7'b1100011; // BEQ/BNE
    parameter HALTOP   = 7'b1111111; // custom HALT

    // Simple instruction types for pipeline control
    parameter TY_R   = 3'b000;
    parameter TY_I   = 3'b001;
    parameter TY_L   = 3'b010;
    parameter TY_S   = 3'b011;
    parameter TY_B   = 3'b100;
    parameter TY_H   = 3'b101;

    // IF stage (clk1)
    always @(posedge clk1) begin
        if (!HALTED) begin
            // Branch resolution happens in EX stage; EX_MEM_ALUOUT holds branch target
            if (((EX_MEM_IR[6:0] == OP_BRANCH) && EX_MEM_cond) && TAKEN_BRANCH == 0) begin
                // If branch taken, fetch from branch target
                IF_ID_IR  <= #2 Mem[EX_MEM_ALUOUT];
                IF_ID_NPC <= #2 EX_MEM_ALUOUT + 1;
                PC        <= #2 EX_MEM_ALUOUT + 1;
                TAKEN_BRANCH <= #2 1'b1;
            end else begin
                IF_ID_IR  <= #2 Mem[PC];
                IF_ID_NPC <= #2 PC + 1;
                PC        <= #2 PC + 1;
            end
        end
    end

    // ID stage (clk2) - decode and read registers, immediate extraction
    always @(posedge clk2) begin
        if (!HALTED) begin
            ID_EX_IR  <= #2 IF_ID_IR;
            ID_EX_NPC <= #2 IF_ID_NPC;

            // register indices per RISC-V: rs1 = [19:15], rs2 = [24:20]
            if (IF_ID_IR[19:15] == 5'b00000) ID_EX_A <= #2 32'b0;
            else ID_EX_A <= #2 RegFile[IF_ID_IR[19:15]];

            if (IF_ID_IR[24:20] == 5'b00000) ID_EX_B <= #2 32'b0;
            else ID_EX_B <= #2 RegFile[IF_ID_IR[24:20]];

            // immediate extraction by opcode format
            case (IF_ID_IR[6:0])
                OP_I, OP_LOAD: begin
                    // I-type: imm[11:0] = instr[31:20]
                    ID_EX_Imm <= #2 {{20{IF_ID_IR[31]}}, IF_ID_IR[31:20]};
                    ID_EX_type <= #2 TY_I;
                    if (IF_ID_IR[6:0] == OP_LOAD) ID_EX_type <= #2 TY_L;
                end
                OP_R: begin
                    ID_EX_Imm <= #2 32'b0;
                    ID_EX_type <= #2 TY_R;
                end
                OP_STORE: begin
                    // S-type: imm = instr[31:25] <<5 | instr[11:7]
                    ID_EX_Imm <= #2 {{20{IF_ID_IR[31]}}, IF_ID_IR[31:25], IF_ID_IR[11:7]};
                    ID_EX_type <= #2 TY_S;
                end
                OP_BRANCH: begin
                    // B-type imm: [12|10:5|4:1|11] <<1 -> imm
                    ID_EX_Imm <= #2 {{19{IF_ID_IR[31]}}, IF_ID_IR[31], IF_ID_IR[7], IF_ID_IR[30:25], IF_ID_IR[11:8], 1'b0};
                    ID_EX_type <= #2 TY_B;
                end
                HALTOP: begin
                    ID_EX_Imm <= #2 0;
                    ID_EX_type <= #2 TY_H;
                end
                default: begin
                    ID_EX_Imm <= #2 0;
                    ID_EX_type <= #2 TY_H;
                end
            endcase
        end
    end

    // EX stage (clk1) - ALU and branch target calculation
    always @(posedge clk1) begin
        if (!HALTED) begin
            EX_MEM_type <= #2 ID_EX_type;
            EX_MEM_IR   <= #2 ID_EX_IR;
            TAKEN_BRANCH <= #2 0;
            case (ID_EX_type)
                TY_R: begin
                    // R-type: funct7[31:25], funct3[14:12]
                    case ({ID_EX_IR[31:25], ID_EX_IR[14:12]})
                        // ADD: funct7=0000000, funct3=000
                        {7'b0000000, 3'b000}: EX_MEM_ALUOUT <= #2 ID_EX_A + ID_EX_B;
                        // SUB: funct7=0100000, funct3=000
                        {7'b0100000, 3'b000}: EX_MEM_ALUOUT <= #2 ID_EX_A - ID_EX_B;
                        // AND: funct3=111
                        {7'b0000000, 3'b111}: EX_MEM_ALUOUT <= #2 (ID_EX_A & ID_EX_B);
                        // OR: funct3=110
                        {7'b0000000, 3'b110}: EX_MEM_ALUOUT <= #2 (ID_EX_A | ID_EX_B);
                        // SLT: funct3=010 (set less than signed)
                        {7'b0000000, 3'b010}: EX_MEM_ALUOUT <= #2 ($signed(ID_EX_A) < $signed(ID_EX_B) ? 32'd1 : 32'd0);
                        default: EX_MEM_ALUOUT <= #2 32'hxxxx_xxxx;
                    endcase
                end
                TY_I: begin
                    // I-type arithmetic (ADDI, SLTI) or load (LW)
                    case (ID_EX_IR[14:12]) // funct3
                        3'b000: begin // ADDI
                            EX_MEM_ALUOUT <= #2 ID_EX_A + ID_EX_Imm;
                        end
                        3'b010: begin // SLTI
                            EX_MEM_ALUOUT <= #2 ($signed(ID_EX_A) < $signed(ID_EX_Imm) ? 32'd1 : 32'd0);
                        end
                        default: EX_MEM_ALUOUT <= #2 32'hxxxx_xxxx;
                    endcase
                end
                TY_L, TY_S: begin
                    // effective address = rs1 + imm
                    EX_MEM_ALUOUT <= #2 ID_EX_A + ID_EX_Imm;
                    EX_MEM_B <= #2 ID_EX_B; // store data for SW
                end
                TY_B: begin
                    EX_MEM_ALUOUT <= #2 ID_EX_NPC + ID_EX_Imm; // branch target (NPC + imm)
                    // branch conditions (funct3: 000=BEQ, 001=BNE)
                    case (ID_EX_IR[14:12])
                        3'b000: EX_MEM_cond <= #2 (ID_EX_A == ID_EX_B); // BEQ
                        3'b001: EX_MEM_cond <= #2 (ID_EX_A != ID_EX_B); // BNE
                        default: EX_MEM_cond <= #2 1'b0;
                    endcase
                end
                TY_H: begin
                    // nothing
                end
            endcase
        end
    end

    // MEM stage (clk2)
    always @(posedge clk2) begin
        if (!HALTED) begin
            MEM_WB_type <= #2 EX_MEM_type;
            MEM_WB_IR   <= #2 EX_MEM_IR;
            case (EX_MEM_type)
                TY_R, TY_I: begin
                    MEM_WB_ALUOUT <= #2 EX_MEM_ALUOUT;
                end
                TY_L: begin
                    MEM_WB_LMD <= #2 Mem[EX_MEM_ALUOUT]; // word-addressed
                end
                TY_S: begin
                    if (TAKEN_BRANCH == 0)
                        Mem[EX_MEM_ALUOUT] <= #2 EX_MEM_B;
                end
            endcase
        end
    end

    // WB stage (clk1)
    always @(posedge clk1) begin
        if (!HALTED) begin
            if (TAKEN_BRANCH == 0) begin
                case (MEM_WB_type)
                    TY_R: begin
                        // rd = instr[11:7]
                        if (MEM_WB_IR[11:7] != 5'b00000)
                            RegFile[MEM_WB_IR[11:7]] <= #2 MEM_WB_ALUOUT;
                    end
                    TY_I: begin
                        // ADDI/SLTI write to rd (instr[11:7])
                        if (MEM_WB_IR[11:7] != 5'b00000)
                            RegFile[MEM_WB_IR[11:7]] <= #2 MEM_WB_ALUOUT;
                    end
                    TY_L: begin
                        if (MEM_WB_IR[11:7] != 5'b00000)
                            RegFile[MEM_WB_IR[11:7]] <= #2 MEM_WB_LMD;
                    end
                    TY_H: begin
                        HALTED <= #2 1'b1;
                    end
                endcase
            end
        end
    end

    // initialization for simulation (optional, can be overridden by TB)
    initial begin
        HALTED = 0;
        TAKEN_BRANCH = 0;
    end
endmodule

```

- Test Bench:

 ```
`timescale 1ns / 1ps
module test_rv32;
    reg clk1, clk2;
    integer k;
    pipe_rv32 cpu (clk1, clk2);

    // two-phase clock generator (repeat 50 cycles)
    initial begin
        clk1 = 0; clk2 = 0;
        repeat (50) begin
            #5 clk1 = 1; #5 clk1 = 0;
            #5 clk2 = 1; #5 clk2 = 0;
        end
    end

    initial begin
        // init registers (x0..x31)
        for (k = 0; k < 32; k = k + 1) cpu.RegFile[k] = k;

        // Program (word-addressed memory). Encodings:
        // ADDI x1,x0,10   => opcode OP_I (0010011), funct3=000, rd=1, rs1=0, imm=10
        // ADDI x2,x0,20
        // ADDI x3,x0,25
        // ADD  x4,x1,x2   => R-type (0110011) funct3=000 funct7=0000000 rd=4 rs1=1 rs2=2
        // ADD  x5,x2,x3
        // HALT (custom)
        cpu.Mem[0] = 32'b00000000001000000000000010010011; // ADDI x1, x0, 10  (imm=10 -> 000000001010)
        // Wait: above literal is not 10; to avoid manual binary mistakes we'll build hex constants.
        // Using easier hex encodings below (I provide correct hex values):
        cpu.Mem[0] = 32'h00A00093; // ADDI x1, x0, 10  (imm=10 => 0x00A)
        cpu.Mem[1] = 32'h01400113; // ADDI x2, x0, 20  (imm=20 => 0x014)
        cpu.Mem[2] = 32'h01900193; // ADDI x3, x0, 25  (imm=25 => 0x019)
        cpu.Mem[3] = 32'h002081B3; // ADD x3? careful: we'll place ADD x4,x1,x2 (rd=4, rs1=1, rs2=2)
        cpu.Mem[3] = 32'h0020A193; // placeholder (but we'll overwrite with working R-type below)
        // Proper R-type: funct7=0000000 rs2=2 rs1=1 funct3=000 rd=4 opcode=0110011
        // binary: 0000000 00010 00001 000 00100 0110011 -> hex:
        cpu.Mem[3] = 32'b0000000_00010_00001_000_00100_0110011; // ADD x4,x1,x2
        cpu.Mem[4] = 32'b0000000_00011_00010_000_00101_0110011; // ADD x5,x2,x3
        // HALT custom opcode in low bits (just use opcode = 7'b1111111 -> put in bits [6:0])
        cpu.Mem[5] = 32'hFFFF_FF7F; // HALT (custom)

        // reset control vars
        cpu.HALTED = 0;
        cpu.PC = 0;
        cpu.TAKEN_BRANCH = 0;

        // wait for program to execute
        #400;

        // display some registers
        for (k = 0; k < 6; k = k + 1)
            $display("x%0d = %0d", k, cpu.RegFile[k]);
    end

    initial begin
        $dumpfile("rv32.vcd");
        $dumpvars(0, test_rv32);
        #600 $finish;
    end
endmodule

```
</details>

--------------------------------------------
<details>
<summary><b>6. Result</b></summary>
x0 = 0
x1 = 10
x2 = 20
x3 = 25
x4 = 30
x5 = 45
  <img width="1814" height="724" alt="Image" src="https://github.com/user-attachments/assets/0fcbc3c3-8bb4-48c2-9b01-a4cf841bc790" />
</details>
