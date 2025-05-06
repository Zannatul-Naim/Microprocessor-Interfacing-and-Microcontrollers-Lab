# 📥 USART3 Receiver - STM32F103C8T6 (Register Level)

This example demonstrates how to **receive characters** over **USART3** on the STM32F103 microcontroller. The code is written using **bare-metal register-level programming**.

---

## 📌 Purpose

- Configure **USART3** (PB10 as TX and PB11 as RX).
- Continuously receive characters sent over serial at **9600 baud**.
- Extract the received character from the **Data Register**.

---

## 🧩 Hardware Connections

| Peripheral | Pin   | Function     |
|------------|-------|--------------|
| USART3_TX  | PB10  | TX (Output)  |
| USART3_RX  | PB11  | RX (Input)   |

---

## 🧾 Code

```c
#include "stm32f1xx.h"  // CMSIS header for STM32F1 series

int main(void)
{
    // 1. Enable Alternate Function IO and GPIOB clocks (bits 0 and 3 in APB2ENR)
    RCC->APB2ENR |= (1 << 0) | (1 << 3); // Enable AFIO + GPIOB

    // 2. Enable USART3 clock (bit 18 in APB1ENR)
    RCC->APB1ENR |= (1 << 18);

    // 3. Clear PB8–PB15 (Control Register High)
    GPIOB->CRH &= ~(0xFFFF);

    // 4. Configure PB10 as AF Push-Pull output (TX), and PB11 as Floating Input (RX)
    GPIOB->CRH |= (0xB << 8);  // PB10: Output 50 MHz, AF Push-Pull
    GPIOB->CRH |= (0x8 << 12); // PB11: Floating Input

    // 5. Set baud rate: BRR = Fclk / baud
    uint32_t system_clock = 36000000;
    uint32_t baud_rate = 9600;
    USART3->BRR = (system_clock + (baud_rate / 2)) / baud_rate;

    // 6. Enable transmitter (TE) and receiver (RE)
    USART3->CR1 |= (1 << 3); // TE
    USART3->CR1 |= (1 << 2); // RE

    // 7. Set word length to 9 bits (optional)
    USART3->CR1 |= (1 << 12);

    // 8. Enable USART3
    USART3->CR1 |= (1 << 13);

    while (1)
    {
        char chat;

        // Wait until reception is complete (RXNE = 1)
        while (!(USART3->SR & (1 << 6)))
        {
            // Wait for received data
        }

        // Read received character from data register
        chat = USART3->DR;
    }
}
