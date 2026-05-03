---
id: firepit
tags: [environmental, fire, hazard, contact]
related: [burn]
---

# Firepit

An environmental hazard. Any entity that enters or remains inside the firepit's area takes fire damage and receives the burn status effect.

## Trigger
- On enter: apply `burn` with fixed value (no attacker stat — use base damage instead)
- On stay: re-apply `burn` each tick to refresh duration and stack intensity

## Applies
- `burn` — value: 5, from: null (fixed, no source stat)

## Notes
The firepit itself has no attack stat, so burn damage is fixed rather than scaling with a caster. Stacking still applies — standing in the fire longer increases burn intensity.
