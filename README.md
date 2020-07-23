# ASSEMBLY
**! KEEP OUT !**  
or enter  
I am a sign, not a cop

## PROCESSOR REGISTERS
### GENERAL REGISTERS
#### DATA REGISTERS
**32-bit:** EAX, EBX, ECX, EDX  
**16-bit:** [A-D]X  
**8-bit:** [A-D]L, [A-D]H

- *AX - primary accumulator* - arithmetic instructions
- *BX - base register* - indexed addressing
- *CX - count register* - stores the loop count in iterative o operations
- *DX - data register* - input/output

#### POINTER REGISTERS
- *IP - Instruction Pointer* - stores the offset address of the next instruction
- *SP - Stack Pointer* - provides the offset value within the program stack
- *BP - Base Pointer* - references the parameter variables passed to a subroutine

#### INDEX REGISTERS
- *SI - Source Index* - source index for string operations
- *DI - Destination Index* - destionation index for string operations

### CONTROL REGISTERS
The 32-bit instruction pointer register and the 32-bit flags register combined are considered as the control registers.

- *OF - Overflow Flag* − indicates the overflow of a high-order bit (leftmost bit) of data after a signed arithmetic operation. 
- *DF - Direction Flag* − determines direction for moving or comparing string. df = 0 L->R, df = 1 R->L
- *IF - Interrupt Flag* − determines whether the external interrupts like keyboard entry, are to be ignored or processed. if = 0 disable, if = 1 enable
- *TF - Trap Flag* − allows setting the operation of the processor in single-step mode, so we could step through the execution one instruction at a time.
- *SF - Sign Flag* − shows the sign of the result of an arithmetic operation. positive -> sf = 0, negative -> sf = 1 
- *ZF - Zero Flag* − indicates the result of an arithmetic or comparison operation. nonzero -> zf = 0, zero -> zf = 1.
- *AF - Auxiliary Carry Flag* − is set when a 1-byte arithmetic operation causes a carry from bit 3 into bit 4.
- *PF - Parity Flag* − indicates the total number of 1-bits in the result obtained from an arithmetic operation. even -> pf = 0, odd -> pf = 1
- *CF - Carry Flag* − contains the carry of 0 or 1 from a high-order bit after an arithmetic operation, stores the contents of last bit of a shift or rotate operation.

### SEGMENT REGISTERS
- *Code Segment* - contains all the instructions to be executed. 16-bit CS register stores the starting address of the code segment.
- *Data Segment* − contains data, constants and work areas. 16-bit DS register stores the starting address of the data segment.
- *Stack Segment* − contains data and return addresses of procedures or subroutines. 16-bit SS register stores the starting address of the stack.

**!** ES, FS and GS provide additional segments for storing data.

## SYSTEM CALLS
- 1 -> sys_exit
- 2 -> sys_fork
- 3 -> sys_read
- 4 -> sys_write
- 5 -> sys_open
- 6 -> sys_close

system call number      -> eax  
arguments               -> ebx, ecx, edx, esi, edi, ebp  
interrupt -> int  

```
mov	edx,4		  ; message length
mov	ecx,msg	      	  ; message to write
mov	ebx,1		  ; file descriptor (stdout)
mov	eax,4		  ; system call number (sys_write)
int	0x80		  ; call kernel
```

## DIRECTIVES

### DEFINE
Used for storage allocation statement for initialized data. 
- DB - Define Byte          - 8 bit
- DW - Define Word          - 16 bit
- DD - Define DoubleWord    - 32 bit  

`msg db 'Hello, world!'`

### RESERVE
Used for reserving space for uninitialized data.
- RESB -	Reserve Byte
- RESW - Reserve Word
- RESD -	Reserve Doubleword  

`msg resb 64`

### TIMES
Allows multiple initializations to the same value. Useful in defining arrays and tables.  

`stars  times  7  db  '*'    ;*******`

### EQU
Defines constants. 

`TOTAL equ 73`

### %assign
Defines numeric constants like. Allows redefinition, case-sensitive. 

`%assign TOTAL 10`

### %define
Defines both numeric and string constants. Allows redefinition, case-sensitive.  

`%define TOTAL 43`

## INSTRUCTIONS

### MOV
Used for moving data from one storage space to another. The value of source operand remains unchanged.  

`MOV destination, source`  

- DEST, SOURCE
- register, register
- register, immediate
- memory, immediate
- register, memory
- memory, register
!! Both the operands in MOV operation should be of same size  

### ARITHMETIC INSTRUCTIONS
#### INC
Increments an operand (8-bit, 16-bit or 32-bit) by one.
```
INC EBX	     ; Increments 32-bit register
```

#### DEC
Decrements an operand (8-bit, 16-bit or 32-bit) by one.  
```
DEC DL       ; Increments 8-bit register
DEC [count]  ; Increments the count variable
```

#### ADD and SUB
Used for performing simple addition/subtraction of binary data in byte, word and doubleword size.  

`ADD/SUB destination, source `

- DEST, SOURCE
- Register to register
- Memory to register
- Register to memory
- Register to constant data
- Memory to constant data

#### MUL and IMUL
Both are used for multiplying binary data. The MUL (Multiply) for unsigned data and the IMUL (Integer Multiply) for signed data.  

`MUL/IMUL multiplier`

- *For two bytes:* The multiplicand is in the AL register, and the multiplier is a byte in the memory or in another register. The product is in AX. High-order 8 bits of the product is stored in AH and the low-order 8 bits are stored in AL

- *For two one-word values:* The multiplicand should be in the AX register and the multiplier is a word. For example, for an instruction like MUL DX, you must store the multiplier in DX and the multiplicand in AX. The resultant product is a doubleword, which will need two registers. The high-order (leftmost) portion gets stored in DX and the lower-order (rightmost) portion gets stored in AX.

- *For two doubleword values:* The multiplicand should be in EAX and the multiplier is a doubleword value. The product generated is stored in the EDX:EAX registers, i.e., the high order 32 bits gets stored in the EDX register and the low order 32-bits are stored in the EAX register.

#### DIV and IDIV
Generates two elements - a quotient and a remainder. The DIV (Divide) for unsigned data and the IDIV (Integer Divide) for signed data.

!! In case of division, overflow may occur. The processor generates an interrupt if overflow occurs.  

`DIV/IDIV divisor`

- *If divisor is byte:* The dividend is assumed to be in the AX register (16 bits). After division, the quotient goes to the AL register and the remainder goes to the AH register.

- *If divisor is word:* The dividend is assumed to be 32 bits long and in the DX:AX registers. The high-order 16 bits are in DX and the low-order 16 bits are in AX. After division, the 16-bit quotient goes to the AX register and the 16-bit remainder goes to the DX register.

- *If divisor is doubleword:* The dividend is assumed to be 64 bits long and in the EDX:EAX registers. The high-order 32 bits are in EDX and the low-order 32 bits are in EAX. After division, the 32-bit quotient goes to the EAX register and the 32-bit remainder goes to the EDX register.

### LOGICAL INSTRUCTIONS
#### AND
It returns 1, if the matching bits from both the operands are 1, otherwise it returns 0.  

`AND AL, 01H	; ANDing with 0000 0001`

#### OR
It returns 0, if both the bits are zero, otherwise it returns 1.  

`OR AL, BL`

#### XOR
If the bits from the operands are same (both 0 or both 1), the resultant bit is cleared to 0, otherwise it sets to 1.  

`XOR EAX, EAX`

#### TEST
Works same as the AND operation, but it does not change the first operand. It can be used to check whether a number in a register is even or odd.  

`TEST AL, 01H`

#### NOT
Reverses the bits in an operand. The operand could be either in a register or in the memory.  

`NOT eax`

## SECTIONS
`section.data`  
constant values, file names, buffer size etc. does not change at runtime

`section.bss`  
variables

`section.text`  
actual code 

```
section	.text
  global _start                 ;must be declared for linker (ld)
	
_start:	                        ;tells linker entry point
  mov	edx,len                 ;message length
  mov	ecx,msg                 ;message to write
  mov	ebx,1                   ;file descriptor (stdout)
  mov	eax,4                   ;system call number (sys_write)
  int	0x80                    ;call kernel

  mov	eax,1                   ;system call number (sys_exit)
  int	0x80                    ;call kernel

section	.data
  msg db 'Hello, world!', 0xa   ;string to be printed
  len equ $-msg                 ;length of the string
```
