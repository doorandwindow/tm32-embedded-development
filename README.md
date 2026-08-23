# STM32 Embedded Development

An AI agent skill package for developing, debugging, reviewing, and documenting STM32 firmware projects across HAL/LL, bare-metal, and RTOS codebases.

It follows the open [Agent Skills](https://agentskills.my/specification) standard (SKILL.md with YAML frontmatter), so it works with any tool that implements the spec — including Claude Code, Cursor, GitHub Copilot, Gemini CLI, OpenAI Codex, Windsurf, and WorkBuddy.

Use this skill when work involves STM32 peripherals, CubeMX-generated code, board bring-up, interrupt/DMA behavior, embedded build/flash, or resource-constrained firmware. It does **not** activate for generic C/C++ work without STM32 context.

## Features

- **Scope-first workflow** — identifies MCU, board, project model, toolchain, and execution model before any change, and keeps unconfirmed facts explicitly marked as "to be confirmed".
- **Hard constraints built in** — ISR rules, DMA/cache coherence, interrupt priority validation (FreeRTOS/CMSIS-RTOS), resource tables, and errata checks for the target silicon revision.
- **Risk-based verification** — five-level verification workflow: static checks → host-runnable unit tests → target build → hardware validation → regression checks, with actual results recorded.
- **Structured delivery** — driver contracts, pin/DMA/IRQ resource tables, resource budgets, test matrices, and known-issues records as the definition of done.
- **No fabrication** — commands that cannot be run or missing hardware are reported as gaps; logs, measurements, and success results are never invented.

## Requirements

- Any AI coding tool that supports the Agent Skills standard (Claude Code, Cursor, GitHub Copilot, Gemini CLI, Codex, Windsurf, WorkBuddy, …)
- STM32 toolchain (e.g. STM32CubeCLI, arm-none-eabi-gcc) — only required if the skill's build/verify commands are used

## Installation

### 1. Get the skill

```bash
git clone https://github.com/doorandwindow/tm32-embedded-development.git
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
cp -r tm32-embedded-development ~/.claude/skills/
```

**Cross-tool tip:** to use the skill in multiple agents at once, symlink the same folder into each tool's skills directory instead of copying:

```bash
ln -s ~/tm32-embedded-development ~/.claude/skills/stm32-embedded-development
ln -s ~/tm32-embedded-development ~/.cursor/skills/stm32-embedded-development
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
