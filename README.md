# Microprocessor-Interfacing-and-Microcontrollers-Lab
CSE 4142 Microprocessor Interfacing and Microcontrollers Lab

# Problem: 1
## (a). Load the Register R0 to R4 with roll numbers of team leaders of five teams. Move the roll numbers stored in R0~R4 to some SRAM area, then move all roll numbers to another area in SRAM

```asm
    ; First, load all roll numbers into registers R0, R1, R2, R3, and R4.
    ; These registers will hold the roll numbers of the team leaders.
    
    LDR R0, =0x77DAA24A     ; Load roll number of team leader 1 into R0
    LDR R1, =0x77DAB248     ; Load roll number of team leader 2 into R1
    LDR R2, =0x77DAD24C     ; Load roll number of team leader 3 into R2
    LDR R3, =0x77DA754F     ; Load roll number of team leader 4 into R3
    LDR R4, =0x89DA3540     ; Load roll number of team leader 5 into R4

    ; Base address of SRAM area 1, where the roll numbers will be stored initially
    LDR R5, =0x20000000     ; Load the base address of SRAM area 1 into R5

    ; Now move all roll numbers from registers (R0-R4) to SRAM area 1 using STM (store multiple)
    STM R5!, {R0-R4}        ; Store the values in R0 to R4 into SRAM area 1 starting at the address in R5
                             ; The "!" after R5 means post-increment, so R5 will be updated after each store operation

    ; Now we will move all the roll numbers from SRAM area 1 to SRAM area 2.
    ; Load the base address of SRAM area 1 and area 2.

    LDR R8, =0x20000000     ; Load the base address of SRAM area 1 into R8 (used to read the values)
    LDR R7, =0x20000020     ; Load the base address of SRAM area 2 into R7 (where the values will be moved)

    ; Load the roll numbers from SRAM area 1 (from address in R8) into registers R0 to R4
    LDM R8, {R0-R4}         ; Load the values from SRAM area 1 into registers R0 to R4

    ; Now store these values (in R0-R4) into SRAM area 2 starting at the address in R7
    STM R7, {R0-R4}         ; Store the roll numbers from registers R0 to R4 into SRAM area 2

```

## (b). Load the Register R0 to R4 with roll numbers of team leaders of five teams. Discard the session and hall code from the roll number, move the roll numbers stored in R0~R4 to some SRAM area.

```asm
    ; The roll numbers contain 10 digits. The first 2 digits represent the session code, 
    ; and the next 3 digits represent the hall code. 
    ; Therefore, a total of 5 digits need to be discarded.
    
    ; We will use bitwise operations to extract only the lower 5 digits of each roll number.
    ; Since the number formed by the remaining 5 digits is greater than 65535 (or 2^16 - 1),
    ; we need to apply a 17-bit mask to extract the relevant part of each roll number.

    AND R5, R5, #0x1FFFF    ; Apply a 17-bit mask (0x1FFFF) to extract the lower 5 digits
    AND R0, R0, R5          ; Mask the roll number in R0 to discard the session and hall codes
    AND R0, R1, R5          ; Mask the roll number in R1 to discard the session and hall codes
    AND R2, R2, R5          ; Mask the roll number in R2 to discard the session and hall codes
    AND R3, R3, R5          ; Mask the roll number in R3 to discard the session and hall codes
    AND R4, R4, R5          ; Mask the roll number in R4 to discard the session and hall codes

    ; Now that we have the correct roll numbers in registers R0 to R4,
    ; we will store these values into a designated SRAM area.

    LDR R6, =0x20000000     ; Load the base address of the SRAM (starting address 0x20000000)
    STM R6, {R0-R4}         ; Store the values in registers R0 to R4 sequentially at the SRAM address

```


# Problem-2: Sorting Operations (Bubble Sort)

## Assume there are 100 integer numbers stored in a specific SRAM area, with each integer having a value ranging from 0 to 255. Sort these numbers in ascending order using Bubble sort.

```asm
    ; The values are stored in contiguous memory locations, starting at address 0x20005000. 
    ; The integer range is from 0 to 255, which can be represented using 8 bits (1 byte).
    ; Therefore, the values are stored in the range from 0x20005000 to 0x20005063.
    ; Let's assume that each address holds random values in the range of 0 to 255 in an unsorted manner.

    ; We will use a sorting algorithm called bubble_sort to sort these values in ascending order.

    LDR R0, =0x20005000    ; Load the base address (start of the memory area with values)
    MOV R1, #99            ; Total numbers to sort (99 values)
    MOV R2, #0             ; Outer loop counter variable (starting at 0)

    outer_loop:
        MOV R3, #0         ; Inner loop counter (starting at 0)
        SUB R4, R1, R2     ; Calculate the remaining number of elements to compare

        inner_loop:
            LDRB R5, [R0, R3]         ; Load the byte (value) at the current index R3
            LDRB R6, [R0, R3, #1]     ; Load the byte (value) at the next index R3 + 1
            CMP R5, R6                ; Compare the two values (R5 and R6)
            ITT HI                    ; If R5 > R6 (Higher than), perform the following instructions
            STRBHI R6, [R0, R3]       ; If R5 > R6, store R6 in the current position (R3)
            ADD R3, R3, #1            ; Increment the inner loop counter R3
            ITLT                     ; If R5 < R6 (Lower than), perform the next instructions
            BLT inner_loop            ; If R5 < R6, continue with the inner loop

        ADD R2, R2, #1             ; Increment the outer loop counter (R2)
        CMP R2, R1                 ; Compare if the outer loop counter has reached the total number of elements (R1)
        BLT outer_loop             ; If R2 < R1, continue with the outer loop
```