# 📤 USART1 Sender - STM32F103C8T6 (Register Level)

This example demonstrates how to **transmit data** using **USART1** on the STM32F103 series microcontroller. The code is written using **register-level programming**, without any HAL or CMSIS driver libraries (except core headers).

---

## 📌 Purpose

- Configure **USART1** (PA9 as TX and PA10 as RX).
- Transmit the character `'A'` continuously.
- Use a **9600 baud rate** with a **36 MHz system clock**.

---

## 🧩 Hardware Connections

| Peripheral | Pin  | Function       |
|------------|------|----------------|
| USART1_TX  | PA9  | TX (Transmit)  |
| USART1_RX  | PA10 | RX (Receive)   |

---

## 🧾 Code

```c
#include "stm32f1xx.h"  // CMSIS header for STM32F1 series

int main(void)
{
    /* -------- 1. Enable Peripheral Clocks -------- */

    // Enable GPIOA (bit 2) and AFIO (bit 0) clocks on APB2
    RCC->APB2ENR |= (1 << 2) | (1 << 0);  // GPIOA + AFIO

    // Enable USART1 clock on APB2 (bit 14)
    RCC->APB2ENR |= (1 << 14);

    /* -------- 2. Configure GPIO Pins PA9 (TX) and PA10 (RX) -------- */

    // Clear PA8–PA15 (CRH: Control Register High)
    GPIOA->CRH &= ~(0xFFFF);

    // Configure PA9 (TX) as Alternate Function Push-Pull Output @ 50 MHz
    GPIOA->CRH |= (0xB << 4);

    // Configure PA10 (RX) as Floating Input
    GPIOA->CRH |= (0x8 << 8);

    /* -------- 3. Configure USART1 -------- */

    // Set baud rate to 9600 with system clock 36 MHz
    uint32_t system_clock = 36000000;
    uint32_t baud_rate = 9600;
    USART1->BRR = (system_clock + (baud_rate / 2)) / baud_rate;

    // Enable Transmitter and Receiver
    USART1->CR1 |= (1 << 3); // TE: Transmitter Enable
    USART1->CR1 |= (1 << 2); // RE: Receiver Enable

    // Set word length to 9 bits (optional)
    USART1->CR1 |= (1 << 12);

    // Enable USART1
    USART1->CR1 |= (1 << 13);

    /* -------- 4. Transmit Data -------- */
    while (1)
    {
        char chat = 'A'; // Character to send

        // Wait until transmission complete (TC = 1)
        while (!(USART1->SR & (1 << 6)))
        {
            // Wait for previous byte to complete
        }

        // Send character
        USART1->DR = chat;
    }
}
