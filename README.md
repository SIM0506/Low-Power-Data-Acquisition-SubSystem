# Autonomous Data Acquisition and Control (DAQ) Subsystem for Phytoplankton Measurement

An ultra-low-power, deterministic embedded data acquisition and control (DAQ) subsystem centered on the **STM32L476RG** microcontroller[cite: 51]. [cite_start]Designed as the supervisory core for an autonomous marine phytoplankton measurement system, this platform manages power gating, implements hardware-in-the-loop fault isolation, and performs precision optical sensor signal processing under tight energy budgets[cite: 141, 142].

## 🚀 Key Features

**Ultra-Low-Power Duty Cycling:** Leverages the STM32 Stop 2 mode (~5 µA standby current) alongside periodic hardware RTC alarms to achieve an estimated **229-day operating lifespan** on a single 1800 mAh battery cell[cite: 52, 83, 87].
**High-Precision Analog Signal Processing:** Features an integrated 12-bit ADC achieving a **72 dB dynamic range**[cite: 54, 89]. [cite_start]Implements a 30-pair oversampling and decimation strategy ($\Delta ENOB = \frac{1}{2}\log_2(N)$) to boost effective resolution to **~14 bits ENOB**, dropping the effective noise floor to $\sim0.2\text{ mV}$[cite: 311, 312].
**Biological Temperature Compensation:** Integrates a high-accuracy I2C MCP9808 temperature sensor ($\pm0.25^\circ\text{C}$ accuracy)[cite: 59, 60]. [cite_start]Implements a first-order biological correction model ($C_{25} = \frac{C_{raw}}{1+\alpha(T-25)}$ where $\alpha = -0.015^\circ\text{C}^{-1}$) based on Cullen and Renger to eliminate non-radiative thermal quenching skew in chlorophyll-a fluorescence readings[cite: 95, 97].
**Deterministic Firmware Architecture:** Structured around rigorous hierarchical state machines governing *Power Sequencing & Health Checks*, *Modulated Sampling & Logging*, and *Fault Handling*[cite: 18, 22, 31, 42].
* **Fault-Tolerant Storage Fallback:** Engineered to survive physical prototyping limitations. When a non-volatile storage exception or filesystem constraint occurs (`FR_DISK_ERR`), the system gracefully degrades to a safe **RAM-resident circular buffer** [cite: 111, 114][cite_start], validating data integrity across power cycles using **CRC-16 validation**[cite: 115].

## 🛠️ Hardware Subsystem Architecture

The physical system is developed inside strict prototyping resource constraints and interfaces via the following topologies:

**Microcontroller:** STM32L476RG (ARM Cortex-M4 @ 4 MHz active baseline)[cite: 51, 83].
**Analog Front-End:** Sallen-Key low-pass input filter feeding a differential ADC setup mapping modulated optical excitation windows[cite: 25, 32].
**Power Domain Isolation:** Dual AP22615AWU precision current-limited load switches independently power-gate the excitation, detection, and storage rails via micro-controller GPIO enables[cite: 65, 72, 74]. [cite_start]Hardware overcurrent flags are linked directly to `EXTI` interrupt lines[cite: 69].
**Thermal Monitoring:** MCP9808 sensor running on an always-on 3.3V rail, utilizing its active-low `ALERT` output pin linked to an `EXTI` line for autonomous hardware thermal-protection[cite: 62, 63, 64].
**Storage Interface:** microSD Card running SPI mode over SPI2 with FatFS abstraction layers[cite: 58, 72].
