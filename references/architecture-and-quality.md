# STM32 Architecture and Quality

Read this page only for new modules, refactoring, driver design, test strategy, quality gates, or errata checks. Follow the current project structure; this page defines boundaries and verifiable properties, not a requirement to rewrite a stable CubeMX project for uniformity.

## Layers and Driver Boundaries

- The application layer owns startup, task creation, use-case orchestration, and recovery policy. Business modules own state machines, control, and protocol semantics. The platform/BSP layer owns STM32 HAL, CMSIS, registers, DMA, IRQs, clocks, and board connections.
- Business modules depend on owned interfaces rather than `SPI_HandleTypeDef`, `UART_HandleTypeDef`, `osThreadId_t`, or register definitions. Interfaces describe operations, timeouts, completion, errors, and ownership without exposing lower-layer handles.
- Bus drivers own peripheral instances, DMA, IRQs, RTOS mutexes, and cache maintenance. Device drivers own device registers, initialization sequences, and data conversion. The BSP owns device wiring and reset/chip-select GPIOs. Do not place PID, filtering, application state machines, or business policy in a device driver.
- Reusable drivers store state in caller-owned contexts and inject the bus implementation through an interface, supporting multiple instances of the same device and host mocks. A singleton hardware resource may remain singleton in the BSP/platform layer, but it must be explicit.
- Reference: public embedded architecture and driver skills treat unidirectional dependencies and bus/device/BSP layering as prerequisites for testability.
  - https://github.com/rovinax/embedded-skills/blob/master/skills/embedded-architecture/SKILL.md
  - https://github.com/rovinax/embedded-skills/blob/master/skills/embedded-driver-design/SKILL.md

## Test Pyramid and Fault Injection

- Host tests cover pure logic, protocol frames, register sequences, parameter bounds, conversions, state machines, and recovery decisions. Mock the bus to control success, NACK, short transfer, busy, timeout, and DMA errors without requiring a board to reproduce those paths.
- Target-board tests cover pins, clocks, real timing, electrical behavior, DMA, cache, reset, low power, and real peripheral compatibility. HIL smoke tests declare the board, firmware version, fixture/peripherals, measurement method, and pass criteria.
- For I2C bus lockup, communication loss, RX overflow, DMA errors, storage full/write failure, task starvation, and watchdog reset risks, record the detection signal, isolation scope, recovery action, retry/backoff limit, and observable evidence. Infinite retry is not a recovery strategy.

## Quality and Observability

- High compiler-warning levels, formatting, and static analysis apply to owned code. Generated code and third-party libraries use pinned versions, separate configuration, and reviewed exceptions; each exception has a rationale, scope, and expiry date or review condition.
- A CI build or reproducible local environment outputs ELF, map, bin/hex, size report, toolchain version, and source commit. Set Flash/RAM baselines or limits appropriate to the project stage; explain threshold exceedances.
- Select a project-supported mechanism for logs, SWO/ITM, RTT, GPIO timing markers, or trace. Record enable conditions, bandwidth/blocking behavior, and real-time effect. Prohibit unbounded formatted or blocking output in ISRs and critical paths.
- Measure critical performance using suitable hardware or core counters: ISR latency/execution time, task period/jitter, bus frame gaps, DMA completion time, and wake time. Test reports record load, sampling point, tools, and acceptance thresholds.
- Reference: the public embedded-systems skill treats stack high-water marks, static analysis, logic analyzers/oscilloscopes, and deadline verification as normal delivery, not optional post-debug activity.
  - https://github.com/Jeffallan/claude-skills/blob/main/skills/embedded-systems/SKILL.md

## Errata Checks

- Confirm the complete ordering code and revision ID from the project, debugger, or chip markings. Consult the matching product page, reference manual, and errata sheet; classify items related to the change as applicable, not applicable, or pending verification.
- “Not applicable” states evidence that its conditions do not hold. “Applicable” states ST’s workaround, software limitation, or hardware limitation and includes it in the test matrix. If the correct revision information or matching errata cannot be obtained, keep the item pending, mark affected hardware validation incomplete, and record it as a risk or blocker.
- Do not substitute the errata for another family/revision or community experience for the target part. Public sources may help locate a problem, but the final conclusion must trace to documentation for the target MCU.
