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