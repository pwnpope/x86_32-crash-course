# Run Down On x86_32 Intel Assembly Course

---

## Course Details:

```markdown 
Registers
Sections
Flags
Functions & Variables
System Calls (syscalls)
Instructions
Calling Conventions
GOT, PLT, GDT
OpCodes
Indexing
```

---

# Registers
### General Purpose Registers:
```nasm
| Register     number        Description
+-------------------------------------------------------------------------------------+
|  EAX     |    0          |     Accumulator                                          |
|  ECX     |    1          |     Counter (Loop counters)                              |
|  EDX     |    2          |     Data (MSB Results from EAX multiply,divide,etc)      |
|  EBX     |    3          |     Base / General Purpose                               |
|  ESP     |    4          |     Current stack pointer                                |
|  EBP     |    5          |     Previous Stack Frame Link                            |
|  ESI     |    6          |     Source Index Pointer – String/Buffer Operations      |
|  EDI     |    7          |     Destination Index Pointer – String/Buffer Operations |
+-------------------------------------------------------------------------------------+

```
### Segment Registers:
```nasm
+------------------------------------------------+
|  Stack Segment (SS)  |   Pointer to the stack  |
+------------------------------------------------+
|  Code Segment  (CS)  |   Pointer to the code   |
+------------------------------------------------+
|  Data Segment  (DS)  |   Pointer to the data   |
+------------------------------------------------+
|  Extra Segment (ES)  |   Pointer to extra data |
+------------------------------------------------+
|  F Segment     (FS)  |   Pointer to extra data |
+------------------------------------------------+
|  G Segment     (GS)  |   Pointer to extra data |
+------------------------------------------------+
```

---

# Sections
```nasm
what are sections? :
    sections are code segments in memory.
    
- section .text = The text section is used for keeping the actual code
    example:
    section .text
        _start:
            mov EAX, 1  ; sys_exit
            mov EBX, 0  ; exit status
            int 0x80    ; interrupt and invoke a sys-call

--------------------------------------------------------------------------------------
- section .data = The data section is used for declaring initialized data or constants
    example:
        section .data
            variable db "this is in section .data", 0x0a, 0x00  ; making a variable with a new line and null terminator
            length equ $ - variable                             ; getting the length of 'variable' and place the result in 'length'

--------------------------------------------------------------------------------------
- section .bss = The bss section is used for declaring variables
    example:
        section .bss
            variable resb 50   ; reserve 50 bytes
            
```

---

# Flags
- #### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) flags are basically background registers with smaller values and act as conditions that are manipulated
- #### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) to read more: https://en.wikipedia.org/wiki/FLAGS_register
- #### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) you do not need to know every flag, you simply need to know what they are and how they work
---
# Functions & Variables
- ### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Functions
```nasm
; functions in assembly are called labels typically
global _start

section .text
    _start:        ; _start label (function)
        call exit  ; calling the exit label (function)
        
    exit:
        mov EAX, 1 ; moves 1 into EAX, sys_exit system call
        mov EBX, 0 ; exit status 0
        int 0x80   ; interrupt the program and invoke a sys-call
```
- ### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) Variables
```nasm
section .data                               ; section .data holds initialized variables
    variable db "this is a variable", 0x00  ; making the variable 'variable' and defining bytes "this is a variable"
```

---

# System Calls (syscalls)
- What is a system call?
    + you could say that a system call is a service requested by the kernel (requested by the program)
    + a fast system call is used to instruct the CPU to add a Procedure/Task Gate Descriptor in either the Global Descriptor Table (GDT) or a Local Descriptor Table (LDT) in order to transition to a lower privilege level (aka user mode to kernel mode).
    + Syscalls are always handled by the EAX(ax) register
    + All syscalls take arguments:
        + EAX, arg0 (sys-call number loaded here)
        + EBX, arg1
        + ECX, arg2
        + EDX, arg3
        + ESI, arg4
        + EDI, arg5
        + EBP, arg6
    + To see a list of system calls please visit: https://syscalls.w3challs.com/?arch=x86
    + Wiki on syscalls: https://en.wikipedia.org/wiki/System_call
---

# Instructions

### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) mov & lea
```nasm
- mov = copies data from one place to another
    example: mov EAX, 1 ; moves the 1 into the EAX register

- lea = ( Load Effective Address ) the 'lea' instruction loads a memory address into another space
    example: lea EAX, some_variable ; loading the memory address of 'some_variable' into the EAX register
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) db, dw, dd, dq & dt
```nasm
- db = bytes are allocated by define bytes ( Define Byte, 1 byte )
    example: variable_name db "Define Bytes!" ; making a variable and 'defining bytes' with the data "Define Bytes!"

-----------------------------------------------------------------------------

- dw = words are allocated by define words ( Define Word, allocates 2 bytes )
    example: message dw "Define Word!" ; making a variable and 'defining words' with the data "Define Word!"

-----------------------------------------------------------------------------

- dd = define double word ( allocates 4 bytes )
    example: variable dd "Define Double Word!" ; making a variable and 'defining double word' with the data "Define Double Word!"

-----------------------------------------------------------------------------

- dq = define quadword ( allocates 8 bytes ) 
    example: variable dq "define quadword!" ; making a variable and 'defining quad-word' with the data "define quadword!"

-----------------------------------------------------------------------------

- dt = Define Ten Bytes ( allocates 10 bytes )
    example: variable dq "define ten-bytes!" ; making a variable and 'defining ten bytes' with the data "define ten-bytes!"

```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) equ
```nasm
- equ = assigns absolute or relocatable values to symbols
    example:
    section .data
        variable_name db "hello world", 0x0a, 0x00 ; setting a variable with the bytes 'hello world' with a new line and terminator after
        length equ $ - variable_name
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) cmp
```nasm
- cmp = compares 2 values and sets background flags to either 1 or 0 depending on the outcome
    example: 
global _start
    section .text
    _start:
        mov EAX, 1  ; moving the value +1 into EAX
        cmp EAX, 0  ; comparing EAX to 0, since we know that EAX has +1 in it than we know that the result of the comparison is 0 (false)
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) call & jmp
```nasm
- call = call & jmp are essentially the same however when call & ret are used together the program
returns to the origanal control flow
    example:
global _start

section .data
        msg db "hello world", 0x0a, 0x00
        length equ $ - msg

section .text
        _start:
                call write    ; write hello world to stdout
                call exit     ; exit the program
        write:
                mov EAX, 4      ; sys_write
                mov EBX, 1      ; stdout
                mov ECX, msg    ; buffer to write
                mov EDX, length ; bytes to read
                int 0x80        ; interrupt the program, invoke sys-call
                ret             ; return to the original control flow

        exit:
                mov EAX, 1 ; sys_exit
                mov EBX, 0 ; exit status
                int 0x80   ; interrupt the program, invoke sys-call
                
-------------------------------------------------------------------

- jmp  = preforms an uncoditional jump, transfers the flow of execution by changing the instruction pointer.
    example:
        _start:
            jmp exit    ; JUMP to the address in memory where the exit label exist
        
        exit:
            mov EAX, 1  ; moves 1 into EAX, (sys_exit system call)
            mov EBX, 0  ; exit status as 0
            int 0x80    ; interrupt the program and invoke a sys-call ( exit the program ) 
        

```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) leave, enter & ret
```nasm
- leave = moves EBP into ESP and pops EBP off the stack
    Equivalent_to:
        mov ESP, EBP  ; copies the data from EBP into ESP
        pop EBP       ; pops EBP off the stack
        
-------------------------------------------------------------------

- enter = pushes EBP onto the stack then copies data within ESP to EBP
    Equivalent_to:
        push EBP      ; push EBP onto the stack
        mov EBP, ESP  ; copy data from whats within ESP to EBP
        
-------------------------------------------------------------------

- ret = Transfers program control to a return address located on the top of the stack
    example:
global _start

section .data
        msg db "hello world", 0x0a, 0x00  ; variable = msg, with string: "hello world" with a new line and null terminator
        length equ $ - msg                ; get the length of the 'msg' variable and give the output to a variable called 'length'

section .text
        _start:
                call write    ; write hello world to stdout
                call exit     ; exit the program
        write:
                mov EAX, 4      ; sys_write
                mov EBX, 1      ; stdout
                mov ECX, msg    ; buffer to write
                mov EDX, length ; bytes to read
                int 0x80        ; interrupt the program, invoke sys-call
                ret             ; return to the original control flow

        exit:
                mov EAX, 1 ; sys_exit
                mov EBX, 0 ; exit status
                int 0x80   ; interrupt the program, invoke sys-call    
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) push & pop ( stack operations )
```nasm
- push = increments ESP by 4(bytes) then places its contents onto the top of the stack where ESP will now point to
    example:
        push EAX      ; push EAX on the stack
        push [EBX]    ; push the 4 bytes at EBX onto the stack

- pop = It first removes the 4(bytes) ESP points to into the specified register or memory location (and then increments ESP by 4)
    example:
        pop EDI     ; pop the top of the stack into EDI
        pop [EBX]   ; pop the top of the stack into memory at the 4 bytes starting at EBX
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+)  nop & int
```nasm
- nop = no operation, when the instruction pointer (EIP) points to this instruction it simply does nothing until the next instruction is hit.
    example:
        _start:
            nop         ; instruction pointer reads this and does nothing
            nop         ; instruction pointer reads this and does nothing
            mov EAX, 1  ; sys_exit system call 
            mov EBX, 0  ; exit status: 0
            int 0x80    ; interrupt and invoke a sys-call
--------------------------------------------------------------------------
- int = interrupt, interrupts the program and notifies the kernel
    example:
        mov EAX, 1 ; sys_exit system call
        mov EBX, 0 ; exit status : 0
        int 0x80   ; interrupt and invoke a sys-call

```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) xor, or, not, test & and (logical instructions)
- xor = exclusive or, bitwise comparison operator
    - XOR is reversable meaning 0x8C ^ 0x2C = 0xA0 and 0x8C ^ 0xA0 = 0x2C
    - xor is a cheap way to encrypt data with a password
    - if the two arguments are the same the output is 0
    - if the two arguments are different the output is 1
    - more on xoring: https://ctf101.org/cryptography/what-is-xor
    - examples:
      ````
        bit1  bit2  equals
         0      0     0
         1      1     0
         1      0     1
         0      1     1
      ````
        - ![alt text](https://i.imgur.com/zHA07QB.png)
        - ![alt text](https://i.imgur.com/B3kGS7E.png)
        - ````nasm
          xor EAX, EAX  ; EAX now equals 0
          ````
---------------------------------------------------------------
- or = the or instruction is used for supporting logical expression by performing bitwise or operation
    - the or instruction is similiar to the xor instruction however it's inclusive un-like the xor instruction
      
    - example:
```nasm
section .text
   global _start 

_start:
   mov    EAX, 5             ; getting 5 in the al
   mov    EBX, 3             ; getting 3 in the bl
   or     EAX, EBX            ; or al and bl registers, result should be 7
   add    EAX, byte '0'      ; converting decimal to ascii

   mov    [result],  EAX    ; move the value of EAX into the first byte of 'result'
   mov    EAX, 4            ; sys_write
   mov    EBX, 1            ; stdout i/o
   mov    ECX, result       ; bytes to write ( to stdout )
   mov    EDX, 1            ; bytes to read
   int    0x80              ; interrupt and invoke a sys-call
    
outprog:
   mov eax, 1  ; system call number (sys_exit)
   mov ebx, 0  ; exit status : 0
   int 0x80    ; interrupt and invoke sys-call

section    .bss
    result resb 1   ; reserving a byte for the variable 'result'
```
---
- not = Performs a bitwise NOT operation (each 1 is set to 0, and each 0 is set to 1) on the destination operand and stores the result in the destination operand location
    - not operation reverses the bits in an operand
    - the operand could be either in a register or in the memory
    - to read more: 
        - https://stackoverflow.com/questions/13597094/implementing-assembly-logical-not-instruction
        - https://x86.puri.sm/html/file_module_x86_id_218.html
        - https://stackoverflow.com/questions/50726304/simple-example-of-not-instruction-in-x86-asm
---
- test = computes the bit-wise logical AND of first operand and the second operand and sets the SF, ZF, and PF status flags according to the result. The result is then discarded
    -  more: https://c9x.me/x86/html/file_module_x86_id_315.html
    -  example:
```nasm
    _start:
        test ECX, ECX    ; set ZF to 1 if ECX == 0
        je _start        ; jump if ZF == 1
        test ECX, ECX    ; set ZF to 1 if ECX == 0
        jne _start       ; jump if ZF != 1
        test EAX, EAX    ; set SF to 1 if eax < 0 (negative)
        js error         ; jump if SF == 1
```
---
- and = it does 32 separate/independent boolean and operations, one for each bit position. (true if both inputs are true, otherwise false.)
    - example:
        - The two numbers are stored in EBX and ECX, the final result obtained after AND operation goes to the EBX register.
```nasm
_start:
    mov EBX, 3527
    mov ECX, 2968
    and EBX, ECX
```    

## Arithmetic Operations
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) add & sub
```nasm
- add = this is going to preform an addition equation
    example: 
        add EAX, 5  ; adds +5 to EAX

- sub = this is going to preform a subtraction equation
    example:
        sub EAX, 5  ; subtracting -5 from EAX 
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) dec & inc
```nasm
- dec = adds +1 to the destination while preserving the state of the carry flag (CF)
    example:
        dec EAX    ; decrement EAX by -1
        
- inc = subtracts -1 from the destination while preserving the carry flag (CF)
    example:
        inc EAX    ; increment EAX by +1
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) adc & sbb
```nasm
- adc = this instruction is just like the add instruction, however it also adds the value of the CF flag
    example:
        adc EAX, 5  ; add 5 plus whatever is in CF to EAX
- sbb = this instruction is just like the sub instruction however you also minus the value of the CF flag
    example:
        sbb EAX, 5  ; subtract 5 plus whatever is in CF from EAX
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) mul, div, imul, idiv & neg
```nasm
- Signed binary integers Signed integers are numbers with a + or - sign
- Signed integers must be sign-extended before division takes place
- signed int:    -32768 to +32767
- unsigned int:  0 to 65535

-------------------------------------------------------------------

- imul = (signed integer multiply) basically the same as the mul instruction but to signed numbers
    example:
        mov EAX, -2      ; placing -2 in EAX
        mov EBX, -100    ; placing -100 in EBX
        imul EBX         ; EBX * EAX = 200 (EAX=200)

-------------------------------------------------------------------

- idiv = (signed integer division)
    example:
        mov EAX, -2    ; moving -2 in EAX
        mov EBX, -100  ; moving -100 in EBX
        idiv EBX       ; EBX / EAX = 50 (EAX=50)

-------------------------------------------------------------------

- mul = (unsigned multiply) this will preform a multiplication equation & apply the output to the EAX register
    example:
        mov EAX, 2      ; placing +2 in EAX
        mov EBX, 100    ; placing +100 in EBX
        mul EBX         ; EBX * EAX = 200 (EAX=200)

-------------------------------------------------------------------

- div = (unsigned divide) this will preform a division equation & apply the output to the EAX register
    example:
        mov EAX, 2      ; placing +2 in EAX
        mov EBX, 100    ; placing +100 in EBX
        div EBX         ; EBX / EAX = 50 (EAX=50)

-------------------------------------------------------------------

- neg = this will make the register negative, or if it is negative it'll make it positive.
    example:
        mov EAX, 50 ; moves +50 into EAX
        neg EAX     ; makes 50 from EAX into -50 so the value of EAX is now equal to -50
-------------------------------------------------------------------
```

### resb, resw, resd, resq & rest  ( Declaring Uninitialized Data )
```nasm
( resb, resw, resd, resq & rest are for the .bss code section, they declare uninitialised storage space )

- resb = reserve a byte
    example:
        section .bss
            variable resb 55    ; reserve 55 bytes into the variable 'variable'
            
----------------------------------------------------------------------------------
- resw = reserve a word
    example:
        section .bss
            variable resw  1   ; reserve one word
            
----------------------------------------------------------------------------------
- resd = reserve a doubleword
    example:
        section .bss
            variable resd 54   ; reserve 54 double words
----------------------------------------------------------------------------------
- resq = reserve a quadword
    example:
        section .bss
            variable resq 7  ; reserve 7 quad-words

----------------------------------------------------------------------------------
- rest = reserve a ten bytes
    example:
        section .bss
            variable rest 5  ; reserve 5 ten bytes
```

## Conditional Jumps
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) jl, jg, jz & je
```nasm
- jl = jump if less than
    example:
        _start:
            mov EAX, 100  ; move 100 into EAX
            cmp EAX, 4    ; compare EAX to 4 background flag will be set to 0 since the result is false
            jl _start     ; jump to start and make an infinite loop basically 

-------------------------------------------------------------------

- jg = jump if greater than
    example:
        _start:
            mov EAX, 100  ; place 100 into the EAX register
            cmp EAX, 150  ; compare EAX to 150 since the result is greater than a background flag will be set to 0
            jg _start     ; jump to _start since the result of the 'cmp' instruction was greater than

-------------------------------------------------------------------

- jz = jump if zero
    example:
        _start:
            mov EAX, 0  ; move zero into EAX
            cmp EAX, 0  ; compare EAX to zero, since EAX=0 the background flag is true
            jz _start   ; jump to _start since EAX=0

-------------------------------------------------------------------

- je = jump if equal to
    example:
        _start:
            mov EAX, 50   ; move +50 into EAX 
            cmp EAX, 50   ; compare EAX to +50
            je _start     ; jump to _start since EAX=50 and we compared it to 50

-------------------------------------------------------------------

```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) jne, jge, jle, jnz 
```nasm
- jne = jump if not equal to
    example:
        _start:
            mov EAX, 133  ; place +133 into EAX
            cmp EAX, 50   ; compare EAX to 50
            jne _start    ; jump to _start since the comparison proved not equal to

-------------------------------------------------------------------

- jge = jump if greater than or equal to
    example:
        _start:
            mov EAX, 144   ; place +144 into EAX
            cmp EAX, 200   ; compare EAX to 200
            jge _start     ; jump since the comparison proved larger than

-------------------------------------------------------------------

- jle = jump if less than or equal to
    example:
        _start:
            mov EAX, 50  ; move +50 into EAX
            cmp EAX, 50  ; compare EAX to 45
            jle _start   ; jump to _start since EAX=50 

-------------------------------------------------------------------

- jnz = not equal to zero
    example:
        _start:
            mov EAX, 33  ; move +33 into EAX
            cmp EAX, 0   ; compare EAX to 0
            jnz _start   ; jump to _start since EAX is not 0
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) jo & jno
```nasm
- flags affected: OF ( overflow flag )
-------------------------------------------------------------------
- jo = jump if an overflow occurs
- jno = jump if no overflow occurs
```
### ![#f03c15](https://via.placeholder.com/15/f03c15/000000?text=+) js & jns
```nasm
- js = this conditional jump will occur if the jump is signed
- jns = this conditional jump will occur  if the jump is not signed
```
---


# Calling Conventions
- What are calling conventions?
    + you could say, calling conventions are the way syscalls take arguments
    + any leftover arguments are pushed onto the stack in reverse order, as in cdecl
    + to read more: https://en.wikipedia.org/wiki/X86_calling_conventions
---
# GOT, PLT & GDT  (tables in memory)
```
- GOT = (Global Offset Table), this is the actual table of offsets as filled in by the linker for external symbols
- PLT = (Procedure Linkage Table), used to call external functions whose address isn't known in the time of linking
- GDT = (Global Descriptor Table) Every 8-byte entry in the GDT is a descriptor, but these descriptors can be references not only to memory segments but also to Task State Segment
```

---
# OpCodes
- Whats an OpCode?
  + OpCodes are simply the byte-code representation of instructions example:
    - inc = 0x40
    - dec = 0x48
  + Wiki: https://en.wikipedia.org/wiki/Opcode
  + list of OpCodes: http://xxeo.com/single-byte-or-small-x86-opcodes

---

# Indexing
```nasm
global _start

section .text
    _start:
        add ESP, 5               ; adding 5 bytes into the ESP register
        mov [ESP],   byte 'H'    ; moving 'H' into index position 0, in the esp register
        mov [ESP+1], byte 'e'    ; moving 'e' into index position 1, in the esp register
        mov [ESP+2], byte 'l'    ; moving 'l' into index position 2, in the esp register
        mov [ESP+3], byte 'l'    ; moving 'l' into index position 3, in the esp register
        mov [ESP+4], byte 'o'    ; moving 'o' into index position 4, in the esp register

        mov EAX, 4             ; sys_write system call
        mov EBX, 1             ; setting 1 as the file descriptor
        mov ECX, ESP           ; bytes to write to stdout
        mov EDX, 5             ; number of bytes the string to write is
        int 0x80               ; interrupt and invoke a system call

        mov EAX, 1 ; sys_exit
        mov EBX, 0 ; exit status
        int 0x80   ; interrupt and invoke sys-call
```
