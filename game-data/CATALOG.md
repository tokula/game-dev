# Game Data Catalog

> Start here. Find the entry you need, then read only that file.

## Status Effects

| id | type | tags | file |
|----|------|------|------|
| burn-dot | debuff | dot, fire | status-effects/burn-dot.json |
| burn-once | debuff | fire | status-effects/burn-once.json |
| regeneration | buff | hot, heal | status-effects/regeneration.json |
| heal | buff | heal, instant | status-effects/heal.json |
| slow | debuff | movement, cc | status-effects/slow.json |
| stun | debuff | control, cc | status-effects/stun.json |
| flinch | debuff | control, interrupt | status-effects/flinch.json |
| freeze | debuff | control, cc, ice, cold | status-effects/freeze.json |
| poison | debuff | dot, poison | status-effects/poison.json |

## Abilities

| id | type | tags | file |
|----|------|------|------|
| fireball | active | fire, projectile, aoe | abilities/fireball.json |
| burn-aura | passive | fire, aoe, aura, contact | abilities/burn-aura.json |
| regen-aura | passive | heal, aoe, aura, support | abilities/regen-aura.json |
| slow-aura | passive | ice, cold, aoe, aura, contact | abilities/slow-aura.json |
| freeze-aura | passive | ice, cold, aoe, aura, contact | abilities/freeze-aura.json |
| poison-dart | active | poison, projectile | abilities/poison-dart.json |
| poison-aura | passive | poison, aoe, aura, contact | abilities/poison-aura.json |
| poison-coating | toggle | poison, weapon-buff, melee, contact | abilities/poison-coating.json |

## Items

| id | category | tags | file |
|----|----------|------|------|
| boiling-flask | consumable | fire, throwable, aoe | items/boiling-flask.json |
| health-potion | consumable | heal, instant, consumable | items/health-potion.json |
| regen-potion | consumable | heal, consumable | items/regen-potion.json |
| poison-flask | consumable | poison, throwable, aoe | items/poison-flask.json |
| antidote-flask | consumable | antidote, cure | items/antidote-flask.json |

## Mechanics

| id | tags | file |
|----|------|------|
| firepit | environmental, fire, hazard, contact | mechanics/firepit.md |
