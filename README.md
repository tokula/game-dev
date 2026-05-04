# game-dev

## Effect flags

Flags are behavioral constraints applied by status effects. Unlike `effects` (which modify stats), flags switch off entire categories of action. They are either on or off — no scaling, no values.

| Flag | What it blocks | Example |
|------|---------------|---------|
| `no_move` | The entity cannot change position | stun, freeze |
| `no_action` | The entity cannot perform any action (attack, use item, etc.) | stun, flinch, freeze |
| `no_cast` | The entity cannot cast abilities | stun, freeze |
| `no_sprint` | The entity cannot sprint but can still walk | — |

**Stacking note:** Flags from multiple active effects combine. If a target has both `slow` (no flags) and `stun` (`no_move`, `no_action`, `no_cast`), all three stun flags are active simultaneously.

**Flags vs effects:** A `speed / multiply` effect slows movement to a fraction of normal. `no_move` stops it entirely. Use effects for gradual or partial impairment; use flags for hard lockouts.

## Race library — provenance tags

Race profile files (under `races/`) tag every source with its licence status. The full registry of known sources lives at `.claude/skills/race-categorizer/references/sources_registry.md`.

| Tag | Meaning |
|-----|---------|
| `[PD]` | Public Domain |
| `[CC0]` | Creative Commons Zero (no rights reserved) |
| `[CC-BY]` | Creative Commons Attribution |
| `[CC-BY-SA]` | Creative Commons Attribution-ShareAlike |
| `[CC-BY-NC]` | Creative Commons Attribution-NonCommercial |
| `[CC-BY-NC-SA]` | Creative Commons Attribution-NonCommercial-ShareAlike |
| `[CC-BY-NC-ND]` | Creative Commons Attribution-NonCommercial-NoDerivatives |
| `[SRD]` | D&D 5e Systems Reference Document (CC-BY-4.0) |
| `[OGL]` | Open Game License |
| `[BLOCKED]` | Confirmed proprietary — excluded from race files |
| `[TAG*]` | Best-guess provenance, not formally verified — confirm before commercial use |