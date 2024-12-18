# Microprocessor-Interfacing-and-Microcontrollers-Lab
CSE 4142 Microprocessor Interfacing and Microcontrollers Lab

# Problem: 1
## (a). Load the Register R0 to R4 with roll numbers of team leaders of five teams. Move the roll numbers stored in R0~R4 to some SRAM area, then move all roll numbers to another area in SRAM

```asm
    ; First, load all roll numbers into registers R0, R1, R2, R3, and R4

    LDR R0, =0x77DAA24A
    LDR R1, =0x77DAB248
    LDR R2, =0x77DAD24C
    LDR R3, =0x77DA754F
    LDR R4, =0x89DA3540

    ; Base address of SRAM area 1
    LDR R5, =0x20000000

    ; Now move all numbers from registers to SRAM area 1 using STM (store multiple)
    STM R5!, {R0-R4}

    ; Then move all roll numbers from SRAM area 1 to SRAM area 2
    LDR R8, =0x20000000
    LDR R7, =0x20000020
    LDM R5, {R0-R4}
    STM R7, {R0-R4}
```

## (b). Load the Register R0 to R4 with roll numbers of team leaders of five teams. Discard the session and hall code from the roll number, move the roll numbers stored in R0~R4 to some SRAM area.

```asm
    ; The roll numbers contain 10 digits. The first 2 digits are the session code, and the next 3 digits are the hall code. 
    ; Therefore, a total of 5 digits need to be discarded.
    ; Here, we will use a bitwise operation to extract only the lower 5 digits.
    ; Since a number with 5 digits is greater than 65535 (or 2^16 - 1), we need to use a 17-bit mask to extract the numbers.

    AND R5, R5, #0x1FFFF    ; Apply a 17-bit mask
    AND R0, R0, R5
    AND R2, R2, R5
    AND R3, R3, R5
    AND R4, R4, R5

    ; Now, store the values into SRAM area

    LDR R6, =0x20000000     ; Base address of SRAM 
    STM R6, {R0-R4}         ; Store R0 to R4 in sequential memory locations
```