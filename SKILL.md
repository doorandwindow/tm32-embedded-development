---
name: stm32-embedded-development
description: >-
  Develop, debug, review, and document STM32 firmware projects across HAL/LL,
  bare-metal, and RTOS codebases, and run the build -> ST-Link flash ->
  serial-capture loop on real hardware (Windows). Use when work involves STM32
  peripherals, CubeMX-generated code, board bring-up, interrupt/DMA behavior,
  embedded build/flash, or resource-constrained firmware, and for any task
  where firmware must reach a real board and be verified through serial/printf
  output — including requests like "编译烧录一下", "下载程序", "上板验证",
  "烧完没反应", "串口没输出/乱码". Interactive GDB breakpoint debugging is out
  of scope. Do not activate for generic C/C++ work without STM32 context.
metadata:
  short-description: STM32 firmware rules, verification, and on-target debug loop
  tags: [STM32, embedded, firmware, HAL, FreeRTOS, DMA, ST-Link, serial, debug-loop]
---

# STM32 Embedded Development

## Goal and Scope

Deliver STM32 firmware changes that are buildable, verifiable, and traceable — from source-level rules to real-board evidence. First identify the project MCU, board, project model, toolchain, and execution model. Keep unknown facts as “to be confirmed”; do not infer them from similar projects, prior memory, or an MCU family name. For real hardware, explicitly mark unavailable board validation as unverified. A successful build is not board-level evidence.

Read [Advanced Verification and Debugging](references/advanced-verification.md) when relevant: FreeRTOS/CMSIS-RTOS interrupt priority or stack issues; Cortex-M7 D-Cache/MPU/DMA; HardFault, reset, low-power wake-up, boot, or production programming. Read [Architecture and Quality](references/architecture-and-quality.md) for new modules, driver design, host testing, code-quality gates, or MCU errata. When a configure/build/flash/capture stage fails, read [Debug-Loop Troubleshooting](references/troubleshooting.md) — a symptom-driven decision tree. Register definitions must always come from the reference manual for the current part number and silicon revision.

## Start Every Task

1. If these workspace fact files exist, read them first and treat them as authoritative:
   - `docs/stm32-ai/project-facts.md`
   - `docs/stm32-ai/hardware-profile.md` (includes the serial capture channel: VID:PID, baud, DTR convention, expected boot banner)
   - `docs/stm32-ai/toolchain-profile.md`
   - `docs/stm32-ai/coding-rules.md`
   If absent, perform read-only discovery from project files and mark the MCU, board, toolchain, and rules as to be confirmed. Create these documents only when the user requests or permits it; skeleton templates are provided in `templates/stm32-ai/`, and a pre-filled example is in `examples/f407-explorer/`.
2. Inspect project entry points and generation boundaries: `*.ioc`, `CMakeLists.txt`, `CMakePresets.json`, `Makefile`, IDE project files, linker scripts, startup files, `Core/`, `Drivers/`, `Middlewares/`, or their project equivalents. Note any project-owned non-interactive automation scripts (e.g. `scripts/build_and_flash.sh`, `scripts/flash.sh`).
3. If present, inspect delivery records in `docs/stm32-ai-engineering/`, especially pin/DMA/IRQ resource tables, driver contracts, resource budgets, test matrices, and known issues. Otherwise list the missing records in the delivery; do not assume their contents.
4. Establish a short list of the goal, affected peripherals/resources, verification method, and risks. If the MCU or toolchain is not confirmed, do read-only discovery and design work only; do not invent build, flash, or debug commands.

## Implementation Decisions

- Follow the existing architecture and API first. Put new code in established user-code sections, extension layers, or driver layers. Edit CubeMX-generated files only in explicitly preserved regions; if the preservation policy is unknown, stop before making generation-sensitive changes.
- Business logic must not directly operate HAL handles. For each driver, state initialization prerequisites, state/thread safety, timeouts, error returns, buffer ownership, and callback execution context.
- Choose HAL, LL, or direct register access according to the existing project. Use LL/register access only for a documented timing, performance, power, or missing-HAL-capability reason, and contain it within a local driver.
- In bare-metal firmware, use explicit events/state machines. In RTOS firmware, use tasks, queues, event groups, mutexes, and notifications to express ownership and synchronization. Do not hide timing problems with busy-waiting.
- Use fixed-width integers, explicit units, and bounds checks. Avoid undocumented narrowing, recursion, large stack objects, and unnecessary dynamic allocation. Define an observable recovery or degradation path for timeouts, overflow, power loss, reset, and resource exhaustion.
- Before changing clocks, validate HSE/HSI, PLL, AHB/APB prescalers, Flash wait states, SysTick, and affected peripheral kernel-clock sources. Do not infer actual UART, ADC, I2C, USB, or FDCAN frequency from `SystemCoreClock` alone.
- Keep `.ioc`, startup files, linker scripts, HAL/CMSIS package versions, and code-generator version control records. Before regeneration, save a reversible state; after regeneration, review the diff, rebuild, and verify interrupts, DMA, clocks, and user-code regions.
- For new modules and material architecture changes, keep dependencies flowing one way: application/task orchestration -> business module -> device driver -> bus interface/bus driver -> BSP/platform implementation. Equivalent existing layers are acceptable. Business modules must not depend on HAL, CMSIS, registers, or FreeRTOS internal types. Do not force a stable existing project into this structure, and do not push structural-refactoring suggestions at stable projects.
- Separate buses, devices, and board resources: the bus layer owns HAL, DMA, IRQs, and bus locks; device drivers depend only on replaceable bus interfaces; the BSP owns concrete board connections. Reusable device drivers use explicit instance contexts rather than hidden global state tied to one peripheral.
- If the project ships its own non-interactive automation scripts, prefer them for that project's flow; otherwise use the skill's `scripts/devloop.py` below (and prefer it whenever a machine-readable result is needed). Interactive, human-attended debugging stays with the user.

## Hardware-in-the-Loop: build -> flash -> capture

Core principle: an agent cannot watch an interactive terminal. All on-target evidence is obtained by time-boxed capture into files, then reading the files. Never hold an interactive serial session; never claim board behavior without a captured log.

Scripts in `scripts/` (`$S` = that directory; Windows 10/11, Python >= 3.10 with pyserial, STM32CubeProgrammer CLI from STM32CubeCLT or the Cube VS Code bundles, CMake+Ninja+GCC project, ST-Link SWD):

| Script | Purpose |
| --- | --- |
| `find_tools.py` | locate cmake/ninja/GCC/Programmer CLI; prints JSON |
| `devloop.py` | one-shot configure -> build -> flash -> capture; stops at the first failing stage with structured JSON |
| `flash.py` | flash only; translates CLI failures into readable hints |
| `serial_capture.py` | capture N seconds of UART into a UTF-8 file; VID:PID auto-detect, own lock file |

Standard loop after a code change:

```
python "$S/devloop.py" --project <project_dir> --seconds 4 --ts
```

Read the final `=== DEVLOOP_RESULT === {...}` line:

- `"stage": "build"` with `errors[]` -> fix the FIRST reported error (file:line given), then re-run; do not fix several at once — chained errors cause misjudgment. `undefined reference` usually means a source file is missing from CMakeLists.
- `"stage": "flash"` with `hint` -> follow the hint (SWD contact, connect-under-reset `mode=UR`, read protection RDP).
- `"stage": "capture"` with `silent: true` -> the port opened but zero bytes arrived; walk the silent-tree in `references/troubleshooting.md`.
- success -> Read `uart.log` (path in `artifacts`), verify the output matches expectations (boot banner, blink counters, ...), then decide whether to iterate.

Logs land in `<project>/build/devloop_last/{configure,build,flash,uart}.log` plus a machine-readable `result.json`. Partial runs: `--no-flash --no-capture` for build-only; standalone `serial_capture.py` to observe longer — it releases the port when done.

Hard rules for the loop:

1. ST-Link and the COM port are exclusive resources. Before a new round, ensure no other holder (VSCode Serial Monitor in the status bar, PuTTY/MobaXterm, a leftover GDB or python serial process); otherwise capture reports Access denied.
2. Do not assert DTR/RTS unless the fact files state the opposite convention; many boards wire reset to DTR.
3. Every "done" claim quotes key lines from `uart.log` — evidence, not adjectives.
4. devloop enforces its own timeouts (build 15 min, flash 3 min); never stack a second run while one is still in flight.
5. The serial capture channel (VID:PID, baud, DTR convention, expected boot banner) lives in `docs/stm32-ai/hardware-profile.md`; recurring board/project pitfalls go to `docs/stm32-ai-engineering/known-issues-and-decisions.md` (Known Issues).

## Hard Constraints

- ISRs only acknowledge, minimally sample, clear/record status, and post an event. Do not block, wait for a lock, allocate dynamically, print, perform long computations, or process business logic directly.
- Interrupt callbacks, RTOS APIs, and HAL APIs must match their execution context. Call RTOS APIs from an ISR only when ISR-safe conditions hold. In FreeRTOS projects, record `__NVIC_PRIO_BITS`, priority grouping, `configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY`/`configMAX_SYSCALL_INTERRUPT_PRIORITY`, and the priority of every IRQ that calls a FromISR API in the resource table. Do not decide based only on prose such as “high” or “low” priority.
- For DMA, define buffer lifetime, producer/consumer ownership, completion and error callbacks, length/alignment, double-buffering strategy, and clean/invalidate coherence on cached Cortex-M devices. Confirm every DMA buffer is in a linker memory region accessible by its DMA master. Maintain cache by address on cache-line boundaries and avoid touching adjacent data still in use. If using an MPU non-cacheable region, record its performance and shared-access impact.
- Whenever adding or changing GPIO, DMA, IRQ, timers, clocks, or memory regions, update the resource table and affected driver contracts. Check alternate-function conflicts, DMA stream/channel/request conflicts, and IRQ-priority constraints.
- For changes to boot, clocks, Flash, DMA, low power, or affected peripherals, determine whether the errata for the current part number and silicon revision apply. Record the conclusion and mitigation in the known-issues/decision record. “Not found” is not a substitute for checking the target revision. If the revision ID or applicable errata cannot be obtained, hardware verification remains incomplete; record it as a risk or blocker.
- Do not perform work with unbounded latency in an ISR, critical section, or high-priority task. Keep critical sections short and state their maximum duration.
- Watchdog, assertion, HardFault, MemManage, and BusFault paths must preserve context or provide a diagnosable signal. Do not swallow errors in an empty loop.
- Without explicit user authorization and a backup of the current configuration, do not change option bytes, read/write protection, Secure/TrustZone state, OTP, or production keys. Treat these as hardware-state migrations and record old value, new value, recovery method, and board impact.

## Verification Workflow

Choose verification depth by risk and record actual results:

1. Static checks: formatting, compiler warnings, unused/uninitialized paths, integer width, concurrent access, out-of-bounds behavior, and error returns. Enable the highest project-supported warnings and static analysis for owned code. Set separate baselines for third-party, CMSIS/HAL, and generated code; do not pass by globally suppressing warnings or mechanically applying `-Werror` to external code.
2. Host-runnable unit tests: hardware-independent logic such as protocol parsing, ring buffers, state machines, and checksums. New device drivers should replace their bus interface so mocks can verify register read/write sequences, conversion, timeout, and error paths; such tests do not replace target-hardware verification.
3. Target build: use the command and configuration registered in `toolchain-profile.md` (or `devloop.py` stages); check MCU macros, optimization level, linker script, map file, Flash/RAM consumption, and stack/heap budget. Compare maps against the baseline and explain material growth.
4. Hardware validation: use the registered flash/debug procedure — on Windows with ST-Link this is the HIL loop above (`devloop.py`) unless `toolchain-profile.md` overrides it. Start with power-up, clocks, reset, and serial logging, then validate peripheral behavior, faults, and repeated boot. For communications and sampling peripherals, test error, timeout, disconnect, or unplug recovery as well as the ideal path. For timing-sensitive paths, obtain evidence with a logic analyzer, oscilloscope, DWT/trace, or the project’s established measurement method. Logs aid diagnosis but do not prove timing. If hardware is unavailable, list unverified items.
5. Regression checks: confirm generation boundaries, startup files, vector table, low-power/wake-up behavior, DMA/IRQ races, and upgrade compatibility remain intact. For clock, cache, or memory-region changes, also test cold boot and repeated resets.

## Debugging Priorities

For a failure, HardFault, timeout, or corrupted data, first classify it as “not running,” “running but not triggered,” “triggered but not completed,” or “completed but corrupted.” Record reset cause, fault registers, PC/LR, active IRQ, clock tree, peripheral status registers, DMA counters, and cache attributes; then inspect power/pin multiplexing, baud rate/timing, interrupt-clear ordering, DMA ownership, and concurrent visibility. Decode faults by mapping the stacked PC/LR to an ELF with debug information, not by printing addresses alone. Prefer non-blocking, bandwidth-controlled trace/log channels; diagnostics must not change critical-path timing. Do not mask the root cause by first changing delays or increasing timeouts. For build/flash/capture-stage symptoms (silent port, garbled output, flash classify errors), follow `references/troubleshooting.md` before touching code.

## Documentation and Delivery

Update applicable material for the change (skeletons for each record are in `templates/delivery/`):

- Architecture or MCU choices: `architecture-brief.md`, `chip-selection-matrix.md`
- Pin, DMA, IRQ, and clock resources: `pin-resource-table.md`
- Driver interfaces and ownership: `driver-contracts.md`
- Stack, heap, Flash, RAM, and real-time budget: `resource-budget.md`
- ISR, DMA, and task relationships: `task-isr-dma-matrix.md`
- Build, flash, smoke-test, fault-injection, and timing results: `build-flash-record.md`, `test-matrix.md`
- Risks, limitations, unresolved issues, and recurring board quirks discovered during debug loops: `known-issues-and-decisions.md`
- When a power target exists, record active/sleep current, wake latency, measurement conditions, and margin in `resource-budget.md`; do not invent a power budget when none is required.
- Only for multicore projects or shared memory across bus masters, record shared regions, MPU/cache attributes, inter-core/host notifications, boot order, barriers, and ownership protocol.

Definition of done: the change scope is clear; build results are reproducible; target-board validation or its absence is recorded; relevant tests, resource records, and interface documentation are updated; generation boundaries remain intact; known risks and rollback are stated.

## Output Requirements

State what changed, why the design was selected, which hardware/concurrency constraints apply, what was verified, and what remains unverified. For commands that cannot be run or missing hardware, state the gap directly; never invent logs, measurements, board models, or successful results.
