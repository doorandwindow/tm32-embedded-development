# STM32 Embedded Development

An AI agent skill package for developing, debugging, reviewing, and documenting STM32 firmware projects across HAL/LL, bare-metal, and RTOS codebases — including a hardware-in-the-loop **build → ST-Link flash → serial capture** loop that lets the agent verify real board behavior by itself (Windows).

It follows the open [Agent Skills](https://agentskills.my/specification) standard (SKILL.md with YAML frontmatter), so it works with any tool that implements the spec — including Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenAI Codex, Windsurf, and WorkBuddy.

Use this skill when work involves STM32 peripherals, CubeMX-generated code, board bring-up, interrupt/DMA behavior, embedded build/flash, or resource-constrained firmware, and for tasks where firmware must reach a real board and be verified through serial output ("编译烧录一下", "上板验证", "烧完没反应"). It does **not** activate for generic C/C++ work without STM32 context, and interactive GDB breakpoint debugging is out of scope.

## Features

- **Scope-first workflow** — identifies MCU, board, project model, toolchain, and execution model before any change, and keeps unconfirmed facts explicitly marked as "to be confirmed".
- **Hard constraints built in** — ISR rules, DMA/cache coherence, interrupt priority validation (FreeRTOS/CMSIS-RTOS), resource tables, and errata checks for the target silicon revision.
- **Hardware-in-the-loop on Windows** — one-shot `devloop.py` runs configure → build → flash → time-boxed UART capture, stops at the first failing stage, and reports a structured JSON verdict with human-readable failure hints (flash error classification, port-busy suspects, silent-boot decision tree).
- **Risk-based verification** — five-level verification workflow: static checks → host-runnable unit tests → target build → hardware validation → regression checks, with actual results recorded.
- **Structured delivery** — driver contracts, pin/DMA/IRQ resource tables, resource budgets, test matrices, and known-issues records as the definition of done.
- **No fabrication** — commands that cannot be run or missing hardware are reported as gaps; logs, measurements, and success results are never invented.

## Requirements

- Any AI coding tool that supports the Agent Skills standard (Claude Code, Cursor, GitHub Copilot, Gemini CLI, Codex, Windsurf, WorkBuddy, …)
- For the source-level workflow: an STM32 toolchain (e.g. STM32CubeCLI, arm-none-eabi-gcc) — only required if the skill's build/verify commands are used
- For the hardware-in-the-loop loop: Windows 10/11, Python ≥ 3.10 with `pyserial`, STM32CubeProgrammer CLI (STM32CubeCLT or the Cube VS Code bundles), a CMake + Ninja + arm-none-eabi-gcc project with `CMakePresets.json`, and an ST-Link SWD connection. Tools are auto-located (VS Code bundles → `C:\ST\STM32CubeCLT` → PATH).

## Installation

### 1. Get the skill

**Important:** the GitHub repository name is `tm32-embedded-development`, but the skill's `name` field is `stm32-embedded-development`. Most agent tools require the folder name to match the `name` field, so pass the target folder name as the second argument of `git clone`:

```bash
git clone https://github.com/doorandwindow/tm32-embedded-development.git stm32-embedded-development
```

If you already cloned without the second argument, rename the folder:

```bash
mv tm32-embedded-development stm32-embedded-development
```

### 2. Install into your agent's skills directory

Copy (or symlink) the `stm32-embedded-development` folder into the skills directory of the tool you use:

| Tool | User-level (all projects) | Project-level (single project) |
| --- | --- | --- |
| Claude Code | `~/.claude/skills/` | `.claude/skills/` |
| Cursor | `~/.cursor/skills/` | `.cursor/skills/` |
| GitHub Copilot | `~/.copilot/skills/` | `.github/skills/` |
| Gemini CLI | `~/.gemini/skills/` | `.gemini/skills/` |
| OpenAI Codex | `~/.codex/skills/` | `.codex/skills/` |
| Windsurf | `~/.codeium/windsurf/skills/` | `.windsurf/skills/` |
| WorkBuddy | `~/.workbuddy/skills/` | `.workbuddy/skills/` |

Example (user-level, Claude Code):

```bash
cp -r stm32-embedded-development ~/.claude/skills/
```

**Cross-tool tip:** to use the skill in multiple agents at once, symlink the same folder into each tool's skills directory instead of copying:

```bash
ln -s ~/stm32-embedded-development ~/.claude/skills/stm32-embedded-development
ln -s ~/stm32-embedded-development ~/.cursor/skills/stm32-embedded-development
```

### 3. Verify

Start a new agent session in an STM32 project and describe a task that should trigger the skill (e.g. "review this UART driver for DMA issues"). The agent should auto-load the skill. If it doesn't, check that the folder name matches the `name` field in `SKILL.md` and that the description has clear trigger conditions.

## Usage

Once installed, the skill activates automatically when a task involves STM32 development, debugging, or review. It guides the workflow through:

1. **Start every task** — read authoritative workspace fact files (`project-facts.md`, `hardware-profile.md`, `toolchain-profile.md`, `coding-rules.md`) or perform read-only discovery.
2. **Implementation decisions** — follow the existing architecture, respect CubeMX generation boundaries, express ownership explicitly in bare-metal or RTOS code.
3. **Verification** — pick verification depth by risk and record actual results.
4. **Delivery** — update the applicable documentation records; state what was verified and what remains unverified.

## Repository Structure

```
stm32-embedded-development/
├── SKILL.md                              # Skill definition: goal, rules, HIL loop, workflow
├── references/
│   ├── advanced-verification.md          # FreeRTOS priorities, cache/MPU/DMA, HardFault, boot, production programming
│   ├── architecture-and-quality.md       # Driver design, host testing, code-quality gates, MCU errata
│   └── troubleshooting.md                # Symptom-driven decision trees for configure/build/flash/capture failures
├── scripts/
│   ├── find_tools.py                     # Locate cmake/ninja/GCC/Programmer CLI; prints JSON
│   ├── devloop.py                        # One-shot configure -> build -> flash -> UART capture (JSON verdict)
│   ├── flash.py                          # Flash only; classifies CLI failures into readable hints
│   └── serial_capture.py                 # Time-boxed UART capture to UTF-8; VID:PID auto-detect + lock file
├── templates/
│   ├── stm32-ai/                         # Workspace fact-file skeletons (project-facts, hardware-profile, toolchain-profile, coding-rules)
│   └── delivery/                         # Delivery record skeletons (pin table, driver contracts, budgets, test matrix, known issues, ...)
├── examples/
│   └── f407-explorer/                    # Pre-filled fact files + pin table + known issues for an STM32F407ZGT6 board (reference, adapt to your project)
└── evals/
    └── evals.json                        # Two end-to-end evaluations (LED blink counter, boot-fault diagnosis) with assertions
```

## Hardware-in-the-Loop (quick start)

After any code change, the agent runs one command and reads the verdict:

```bash
python "$S/devloop.py" --project D:/path/to/project --seconds 4 --ts
```

The final `=== DEVLOOP_RESULT === {...}` line names the failing stage (`build` with parsed `file:line` errors, `flash` with translated causes, `capture` with a `silent` flag) or reports success with the `uart.log` path. Full logs land in `<project>/build/devloop_last/`. Failure symptoms are walked through `references/troubleshooting.md`. The serial channel (VID:PID, baud, DTR convention, expected boot banner) is recorded per project in `docs/stm32-ai/hardware-profile.md`.

## Templates

Copy the skeletons you need into the target project — no need to write them from scratch:

- `templates/stm32-ai/` — the four workspace fact files read at the start of every task (`docs/stm32-ai/`). Fill them once per project; the skill then treats them as authoritative.
- `templates/delivery/` — the delivery records referenced by the skill's Documentation and Delivery section (`docs/stm32-ai-engineering/`). Update the applicable ones per change.
- `examples/f407-explorer/` — pre-filled records for a real STM32F407ZGT6 (ALIENTEK Explorer V2.2) project with MPU6050 + NT35510 LCD + GT9147 touch: the four fact files, a `pin-resource-table.md` (full board pin map with conflicts and jumper matrix, verified against ALIENTEK's official IO table and BSP sources), a pre-filled `known-issues-and-decisions.md` (board quirks, Keil-vs-GCC printf redirect, shared software-I2C bus), and a local reference library (RM0090, datasheets, schematic paths). Unconfirmed values are marked `TBC` — verify against your own schematic and code before use.

## Documentation

| Document | Covers |
| --- | --- |
| [SKILL.md](SKILL.md) | Goal and scope, per-task workflow, implementation decisions, HIL loop, hard constraints, verification, debugging priorities, delivery requirements |
| [references/advanced-verification.md](references/advanced-verification.md) | FreeRTOS/CMSIS-RTOS interrupt priority and stack issues; Cortex-M7 D-Cache/MPU/DMA; HardFault, reset, low-power wake-up, boot, production programming |
| [references/architecture-and-quality.md](references/architecture-and-quality.md) | New modules, driver design, host testing, code-quality gates, MCU errata |
| [references/troubleshooting.md](references/troubleshooting.md) | Symptom-driven decision trees: configure/build error tables, flash failure classification, silent-port and garbled-output diagnosis |

## Evals

[evals/evals.json](evals/evals.json) defines two end-to-end tasks that require a real board:

| id | Scenario | Verifies |
| --- | --- | --- |
| 0 | `add-led-blink-counter` | implement LED blink + counter printf, flash, capture ≥3 strictly increasing `blink=N` lines |
| 1 | `diagnose-boot-fault` | diagnose a silent boot (early HardFault), fix, re-flash, capture restored boot output |

Fixture paths in the file are author-local examples — point them at your own fixture projects before running.

## Contributing

Contributions are welcome. Please keep the following in mind:

- Preserve the skill's core principles: evidence-based verification, no fabricated results, explicit "to be confirmed" markers.
- Keep register/errata guidance tied to reference manuals for specific part numbers and silicon revisions.
- Update the references and `SKILL.md` consistently when adding or changing behavior.

## License

Released under the [MIT License](LICENSE).
