# STM32 Embedded Development

A [WorkBuddy](https://www.workbuddy.cn) skill package for developing, debugging, reviewing, and documenting STM32 firmware projects across HAL/LL, bare-metal, and RTOS codebases.

Use this skill when work involves STM32 peripherals, CubeMX-generated code, board bring-up, interrupt/DMA behavior, embedded build/flash, or resource-constrained firmware. It does **not** activate for generic C/C++ work without STM32 context.

## Features

- **Scope-first workflow** — identifies MCU, board, project model, toolchain, and execution model before any change, and keeps unconfirmed facts explicitly marked as "to be confirmed".
- **Hard constraints built in** — ISR rules, DMA/cache coherence, interrupt priority validation (FreeRTOS/CMSIS-RTOS), resource tables, and errata checks for the target silicon revision.
- **Risk-based verification** — five-level verification workflow: static checks → host-runnable unit tests → target build → hardware validation → regression checks, with actual results recorded.
- **Structured delivery** — driver contracts, pin/DMA/IRQ resource tables, resource budgets, test matrices, and known-issues records as the definition of done.
- **No fabrication** — commands that cannot be run or missing hardware are reported as gaps; logs, measurements, and success results are never invented.

## Installation

### Prerequisites

- [WorkBuddy](https://www.workbuddy.cn) desktop app (Windows / macOS)
- STM32 toolchain (e.g. STM32CubeCLI, arm-none-eabi-gcc) — only required if the skill's build/verify commands are used

### Install as a user-level skill (all projects)

```bash
git clone https://github.com/doorandwindow/tm32-embedded-development.git
cp -r tm32-embedded-development ~/.workbuddy/skills/
```

### Install as a project-level skill (single project)

```bash
cp -r tm32-embedded-development <your-project>/.workbuddy/skills/
```

## Usage

The skill activates automatically when a task involves STM32 development, debugging, or review. It guides the workflow through:

1. **Start every task** — read authoritative workspace fact files (`project-facts.md`, `hardware-profile.md`, `toolchain-profile.md`, `coding-rules.md`) or perform read-only discovery.
2. **Implementation decisions** — follow the existing architecture, respect CubeMX generation boundaries, express ownership explicitly in bare-metal or RTOS code.
3. **Verification** — pick verification depth by risk and record actual results.
4. **Delivery** — update the applicable documentation records; state what was verified and what remains unverified.

## Repository Structure

```
tm32-embedded-development/
├── SKILL.md                              # Skill definition: goal, rules, workflow
└── references/
    ├── advanced-verification.md          # FreeRTOS priorities, cache/MPU/DMA, HardFault, boot, production programming
    └── architecture-and-quality.md       # Driver design, host testing, code-quality gates, MCU errata
```

## Documentation

| Document | Covers |
| --- | --- |
| [SKILL.md](SKILL.md) | Goal and scope, per-task workflow, implementation decisions, hard constraints, verification, debugging priorities, delivery requirements |
| [references/advanced-verification.md](references/advanced-verification.md) | FreeRTOS/CMSIS-RTOS interrupt priority and stack issues; Cortex-M7 D-Cache/MPU/DMA; HardFault, reset, low-power wake-up, boot, production programming |
| [references/architecture-and-quality.md](references/architecture-and-quality.md) | New modules, driver design, host testing, code-quality gates, MCU errata |

## Contributing

Contributions are welcome. Please keep the following in mind:

- Preserve the skill's core principles: evidence-based verification, no fabricated results, explicit "to be confirmed" markers.
- Keep register/errata guidance tied to reference manuals for specific part numbers and silicon revisions.
- Update the references and `SKILL.md` consistently when adding or changing behavior.

## License

This project is currently **unlicensed** — all rights reserved by the author. A license will be added once the author selects one.

---

*Part of the WorkBuddy skills ecosystem.*
