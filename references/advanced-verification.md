# STM32 Advanced Verification and Debugging

Read this page only when its features are involved. All register bits, memory domains, cache-line lengths, DMA reachability, and boot configuration must be verified against the reference manual for the current STM32 part number and silicon revision.

## CubeMX and Generated Code

- Review the `.ioc` alongside generated output. Put application logic in `/* USER CODE BEGIN */`/`END` markers or a project-defined extension layer. Extend middleware files without preservation boundaries through wrapping or a patch mechanism instead of direct edits.
- Save a reversible version before regeneration. Review the diff afterward, focusing on clocks, GPIO alternate functions, DMA request/stream assignments, IRQ handlers, `HAL_MspInit`, linker inputs, and middleware configuration.
- Rationale: ST support material confirms that the generator preserves only explicit user-code sections; other manual changes can be overwritten.
  - https://community.st.com/t5/stm32cubemx-mcus/i-lost-all-my-code-after-regeneration-using-cubemx/td-p/29195

## FreeRTOS and Interrupts

- A smaller Cortex-M numerical priority is logically higher. Do not confuse the unshifted library priority passed to `NVIC_SetPriority()` with its shifted register value.
- Every IRQ that calls `...FromISR()` must comply with the FreeRTOS syscall-priority boundary. Record its actual priority and the basis for the decision in the task/IRQ table. Higher-priority IRQs that cannot call RTOS APIs perform minimal work and hand off to an allowed context.
- Enable `configASSERT` and, when relevant, stack-overflow detection. Validate each task stack budget with high-water-mark or equivalent runtime measurement; static configuration alone is insufficient.
- Rationale: the FreeRTOS Cortex-M port documentation defines the interrupt-priority limits for calling RTOS APIs.
  - https://freertos.org/RTOS-Cortex-M3-M4.html

## DMA, Memory Domains, D-Cache, and MPU

- Before starting DMA, confirm its controller can access the RAM region containing the buffer. On MCUs with multiple RAM domains, do not assume every DMA controller can access all SRAM, TCM, or external RAM.
- For transmit DMA (memory to peripheral), clean the relevant range before starting it. For receive DMA (peripheral to memory), use a documented strategy: for cacheable memory, before starting DMA clean+invalidate or invalidate an already cached, potentially dirty, aligned dedicated buffer as appropriate to the chosen strategy, then invalidate the aligned received range again before the CPU reads it; alternatively put the buffer in an appropriate non-cacheable MPU region. Cache maintenance must cover cache-line-aligned ranges and isolate or protect adjacent objects, so rounding the range down/up cannot corrupt cache lines still in use.
- Select a non-cacheable MPU region, dedicated aligned buffers, or cache maintenance, and record the choice in the driver contract. Do not routinely clean/invalidate the entire D-Cache for the system lifetime.
- Shared data between cores or DMA masters also needs an explicit visibility, barrier, cache, and handshake protocol. `volatile` does not solve cache coherence or ownership.
- Rationale: ST AN4839 explains L1-cache/DMA coherency for F7/H7; CMSIS supplies range-based D-Cache maintenance APIs.
  - https://www.st.com/resource/en/application_note/dm00272913-level-1-cache-on-stm32f7-series-and-stm32h7-series-stmicroelectronics.pdf
  - https://arm-software.github.io/CMSIS_6/latest/Core/group__Dcache__functions__m7.html

## Faults, Reset, and Observability

- A fault handler captures at least the stack frame (`r0-r3`, `r12`, `LR`, `PC`, `xPSR`), SCB fault-status registers, and reset cause. Persist the record only when storage or the logging channel is reliable. Avoid blocking prints in fault context.
- Confirm build artifacts retain an ELF and map suitable for symbolization. Each flashed image records its Git commit, build configuration, and toolchain version so PC/LR can be mapped to source.
- Watchdog resets must have an identifiable cause. When validating deliberate deadlock, task starvation, or lost communication, confirm that the system enters the expected recovery state.

## Clock, Low Power, and Boot

- Validate the clock tree from registers or tool output, not configuration code alone. Check PLL lock, supply/voltage range, Flash wait states, AHB/APB prescalers, and every peripheral kernel-clock mux.
- Low-power changes must validate entry conditions, GPIO leakage, wake sources and flag-clear order, clock recovery, peripheral reinitialization, and repeated entry/exit. Validate peripherals with independent clock domains, such as RTC/LSE, USB, and ADC, separately.
- Boot or linker-script changes must validate vector-table location, VTOR where applicable, initial stack, `.data` copy, `.bss` clear, C/C++ initialization, and interrupt-vector overrides.

## Production and Irreversible Configuration

- Before changing option bytes, read/write protection, TrustZone, secure boot, OTP, or keys, read and preserve current values, confirm the target part and board, explain recoverability, and obtain explicit authorization.
- After enabling protection, validate debug, firmware update, production programming, and repair paths on an independent board or approved recovery flow. One successful development-board run is not production evidence.
