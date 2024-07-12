# Introduction

The R-machine is a fairly simple RISC CPU that can be emulated in software, intended to help students learn about computer architecture, machine code, assembly language, and related concepts.

# Architecture

The 32-bit version of the R-machine (abbreviated *TRM32*) has the following 32-bit registers:

| ID | Name    | Purpose |
| -- | --------| ------- |
| 0000 | `x0` | Always zero |
| 0001 - 1101 | `a0`-`a13` | User registers |
| 1110 | `ra`    | Return address |
| 1111 | `sp`    | Stack pointer |

# Instruction encoding

Each instruction is 32 bits in length and must be aligned on a 4-byte boundary in memory.

Instructions have the following format:

| Bits    | Subfield |
| --------| ------- |
| 0-4     | Opcode |
| 5-8     | Destination register ID (*rd*) |
| 9-12    | Source register 1 ID (*rs1*) |
| 13-16   | Source register 2 ID (*rs2*) |
| 17-31   | Immediate value (*imm*) |

The instructions are as follows:

| Opcode  | Instruction | Description |
| --------| ------- |-----|
| 00000 | - | Invalid instruction |
| 00001 | LI | Set *rd* to *imm* |
| 00000 | ADD | Add *imm* to *rs1*+*rs2* |
| 00000 | AND | Bitwise AND *rs1* with *rs2* |
| 00000 | ANDI | Bitwise AND *rs1* with *imm* |
| 00000 | OR | Bitwise OR *rs1* with *rs2* |
| 00000 | ORI | Bitwise OR *rs1* with *imm* |
| 00000 | XOR | Bitwise XOR *rs1* with *rs2* |
| 00000 | XORI | Bitwise XOR *rs1* with *imm* |
| 00000 | SUB | Subtract *imm*+*rs2* from *rs1* |
| 00000 | SHL | Shift *rs1* left by *rs2*+*imm* bits |
| 00000 | SHR | Shift *rs1* right by *rs2*+*imm* bits |
| 00000 | JMP | Unconditional jump to `pc`+*imm* |
| 00000 | JMPL | Unconditional jump to 32-bit address in next word |
| 00000 | RET | Return to address saved in *ra* from previous jump |
| 00000 | BEQ | Conditional branch to `pc`+*imm* if *rs1* == *rs2* |
| 00000 | BNE | Conditional branch to `pc`+*imm* if *rs1* != *rs2* |
| 00000 | BLT | Conditional branch to `pc`+*imm* if *rs1* < *rs2* |
| 00000 | BGE | Conditional branch to `pc`+*imm* if *rs1* >= *rs2* |
| 00000 | PUSH | Push value in *rs1* to stack, adjusting *sp* |
| 00000 | POP | Move value from stack to *rd*, adjusting *sp* |
| 00000 | LOAD | Copy value from memory address *imm*+*rs1*+*rs2* |
| 00000 | STORE | Copy value from *rs2* to memory address *imm*+*rs1*+*rs2* |
| 00000 | ECALL | Make a call to the surrounding execution environment |
| 00000 | EBREAK | Transfer control back to debugging environment |
| 00000 | - | Unused |

# Examples

Print the character 'a' to the serial device:

```
    li a0, 'a' ; load a0 with character 'a'
    li a7, 1   ; system call number 1 ('putchar')
    ecall      ; execute system call
    ebreak     ; exit to debugger
```

Print the string "hello world" to the serial device:

```
    load a1, helloworld ; address of first char
    add a2, a1, 12      ; address of last char
    li a7, 1            ; system call number 1 ('putchar')
.loop
    load a0, a1, 0      ; next character
    ecall               ; execute system call
    add a1, a1, 1       ; increment 'next char' pointer
    bne a1, a2, loop    ; loop until end
    ebreak              ; exit

.helloworld
    data "Hello world!"
```

# Notes

https://riscv.org/wp-content/uploads/2017/05/riscv-spec-v2.2.pdf

https://marz.utk.edu/my-courses/cosc230/book/example-risc-v-assembly-programs/

https://users.sussex.ac.uk/~mfb21/compilers/slides/11-handout.pdf