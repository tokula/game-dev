# Claude Code — Game Knowledge Base

## Token budget reminder

CATALOG.md + one entity file ≈ 60–120 tokens. Do not read whole folders unless explicitly asked to list all entries of a type.

## How to use game-data/

All game design data lives in `game-data/`. Always follow this two-step lookup to stay token-efficient:

1. **Read `game-data/CATALOG.md` first** — it's a compact index of every entity. Use it to find the file path for the entry you need.
2. **Read only the specific file** — never load an entire category folder.

For tag-based searches, Grep `CATALOG.md` directly (e.g. search for `fire` to find all fire-tagged entries).

## Directory layout

```
game-data/
  _schemas/           JSON Schema definitions — the shape every entity must follow
  status-effects/     One .json file per status effect (buff/debuff/dot/aura)
  abilities/          One .json file per ability (active/passive/toggle)
  items/              One .json file per item (weapon/armor/consumable/accessory)
  mechanics/          One .md file per game system (freeform prose + YAML frontmatter)
  CATALOG.md          Lightweight index — start here
```

## Adding a new entry

1. Consult `_schemas/<type>.schema.json` for the required fields.
2. Create `game-data/<category>/<id>.json` (or `.md` for mechanics).
3. Append a row to the correct table in `game-data/CATALOG.md`.

## Schemas

| Type | Schema file |
|------|-------------|
| Status effect | `_schemas/status-effect.schema.json` |
| Ability | `_schemas/ability.schema.json` |
| Item | `_schemas/item.schema.json` |
| Mechanic | `_schemas/mechanic.schema.json` (frontmatter template) |

## Naming conventions

Inconsistent naming causes lookup errors and bad cross-references. Follow these rules when adding any entry:

**Abilities are named after the effect they apply, not the element that delivers it.**
- `burn-aura` not `fire-aura` — the aura applies `burn-dot`; the element goes in tags
- `regen-aura` not `healing-aura` — the aura applies `regeneration`
- Exception: named spells with no direct effect mapping (e.g. `fireball`) use their in-world name

**Items are named after their real-world identity, not their mechanical effect.**
- `health-potion` (instant HP restore) vs `regen-potion` (applies regeneration over time)
- The name should tell a player what it *is*, the `applies` field tells Claude what it *does*

**Status effects own their element only if the element is intrinsic to the effect.**
- `burn-dot` and `burn-once` both have `"fire"` in tags — burning is inherently fire
- `freeze` has `"ice"` in tags — freezing is inherently ice
- `slow` does NOT have `"ice"` in tags — slow is element-neutral; mud, gravity, and nets also slow

**When an effect can be applied in two distinct ways, split it into two files.**
- `burn-dot` (damage over time) and `burn-once` (single flash) are separate entries
- `heal` (instant restore) and `regeneration` (heal over time) follow the same pattern
- Sources use `applies` for mandatory effects and `may_apply` for a pool the AI/context selects from

**Tags describe the entry itself, not its source.**
- An effect's tags describe what the effect is, not what caused it
- A source (ability/item/mechanic) can carry element tags independently

**Every status effect must include an `effects` array.**
- Use `[]` when the effect works purely through `flags` (e.g. stun, flinch, freeze)
- Never omit the field — a missing `effects` key is a schema error, not the same as no stat change


