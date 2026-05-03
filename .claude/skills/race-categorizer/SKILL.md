---
name: race-categorizer
description: Categorize fictional or mythological races/species used in games (such as Valkyries, Orcs, Leprechauns, Dwarves, Vampires, Tieflings) into structured Markdown profile files following a fixed template. Use this skill whenever the user wants to research, document, classify, or build a reference library of game races, mythological beings, fantasy species, monster types, or playable races, even if they don't explicitly say "categorize" — phrases like "make a race profile for X", "add Orcs to my races folder", "look up Valkyries", or "what kind of creature is a Kitsune" should all trigger this skill. Also trigger for batch requests like "Valkyries, Orcs, Leprechauns" and for races with multiple cultural origins (e.g. Dwarves exist in both Norse and Slavic myth — produce one file per origin).
---

# Race Categorizer

Build structured Markdown profiles of mythological and fictional races for game-design reference. Each profile follows a fixed template and is saved as a `.md` file in a races library folder.

## Workflow

1. **Parse input** — accept a single name, comma-separated list, or newline-separated list. Treat each entry independently.
2. **Validate each entry** — distinguish races from individual figures and from unique beings (see "Input validation").
3. **For each valid race entry, identify source variants** — if the race appears in multiple distinct mythologies (e.g. Dwarves in Norse vs. Slavic), produce one file per source. See "Multiple source mythologies".
4. **Research each variant** — use web search for general details, etymology, named individuals attested in primary sources, and trademark verification. Flag conflicting sources.
5. **Present sources for approval** — before writing anything, list every source found for the variant and ask the user to confirm which to keep. Format:

   ```
   Sources found for Valkyrie (Norse):
     [1] Völuspá, Poetic Edda (c. 10th–13th century) — names six Valkyries, primary cosmological role
     [2] Grímnismál, Poetic Edda — lists thirteen named Valkyries
     [3] Gylfaginning, Prose Edda, Snorri Sturluson (c. 1220) — battlefield role description
     [4] World History Encyclopedia: "Valkyrie" — secondary overview
     [5] God of War Wiki — game depiction details

   Use all? Or discard any? (enter numbers to discard, or press enter to use all)
   ```

   Only proceed to step 6 once the user has confirmed. Omit discarded sources from all file content.

6. **Fill the template** for each variant. The template lives at `references/template.md` — read it and follow it exactly. Also prepare a companion `{name}_{source}_roster.md` (see "Roster file").
7. **Write files** to `./races/` (default) using the filename convention. Write both the main profile and the companion roster file. Handle existing files via diff prompt.
8. **Summarize** what was created at the end (list files written, files skipped, any flags).

## Input validation

Three categories of input:

**(a) Valid race/species** — proceed normally. Examples: Valkyries, Orcs, Leprechauns, Dwarves, Vampires, Kitsune, Tieflings.

**(b) Individual named figure** — reject and suggest the parent race. Ask inline: "Did you mean [parent race]? (y/n)". If yes, proceed with the parent race. If no, skip this entry.
- Thor → suggest Æsir
- Medusa → suggest Gorgon
- Odin → suggest Æsir
- Zeus → suggest Olympians / Greek gods
- Anubis → suggest Egyptian gods

**(c) Unique being with no parent race** — there is no "race" to categorize, but suggest the closest race-like grouping(s). Ask inline: "There's no race for [X]. Closest groupings: [A], [B], [C]. Use one of these? (pick one or skip)".
- Fenrir → "jötnar offspring" or "monstrous wolves of Norse myth"
- Jörmungandr → "jötnar offspring" or "primordial serpents"
- Cerberus → "monstrous hounds of Greek underworld"

If the user is unsure or the input is ambiguous, ask before proceeding rather than guessing.

## Multiple source mythologies (Option D)

If a race name appears in genuinely different mythologies, produce **one file per mythological origin**. A "different mythology" means a different cultural source layer — not just a different game-system depiction.

**Examples:**
- "Dwarf" → `dwarf_norse.md` and `dwarf_slavic.md` (two distinct mythologies)
- "Elf" → `elf_norse.md` (the álfar) and `elf_celtic.md` (sídhe-adjacent traditions where applicable)
- "Giant" → `giant_norse.md` (jötnar), `giant_greek.md` (gigantes, titans handled separately)

Game-specific variants (Tolkien dwarves, D&D dwarves, Warhammer dwarves) do **not** get separate files — they go in the "Game depictions" section inside the relevant mythology file. Tolkien dwarves go inside `dwarf_norse.md` since they descend from Norse myth.

If a race has only one mythological origin (Valkyries are purely Norse, Leprechauns are purely Irish/Celtic), produce a single file.

If unsure how many variants to produce, list the candidates to the user and confirm before generating files.

## Filename convention

- Format: `{name}_{source}.md`
- All lowercase
- Spaces and hyphens replaced with underscores
- Source uses the short form: `norse`, `greek`, `slavic`, `celtic`, `mesopotamian`, `egyptian`, `hindu`, `east_asian`, `african`, `mesoamerican`, `polynesian`, `judeo_christian`, `modern_literary`, `original`
- Single-origin races still get the source suffix (e.g. `valkyrie_norse.md`, `leprechaun_celtic.md`)

**Examples:**
- Valkyries → `valkyrie_norse.md`
- Mind Flayers → `mind_flayer_modern_literary.md`
- Kitsune → `kitsune_east_asian.md`

## Output folder

Default: `./races/` in the current working directory. Create it if it doesn't exist. If the user specifies a different folder, use that instead.

## Roster file

Every race profile gets a companion `{name}_{source}_roster.md` written alongside it. Cross-link both files with a `> See also:` line at the top of each.

**Filename:** same base as the race file with `_roster` appended — e.g. `valkyrie_norse_roster.md`, `dwarf_norse_roster.md`.

**Structure:**

```markdown
# {Race} — {Source} Roster

> See also: [{name}_{source}.md]({name}_{source}.md)

## Command hierarchy

{Tree or prose describing the authority structure within and above the race.
Include non-race superiors if relevant (e.g. Odin above the Valkyries).
Note if no formal internal hierarchy is documented.}

## Named individuals

Prefer names attested in primary mythological sources — they are in the public domain and
carry cultural weight. Game-invented names are included for reference only, clearly labelled.

| Name | Source | Rank / role | Notes |
|------|--------|-------------|-------|
| {Name} | {Primary text (myth)} or {Game: title (game)} | {Rank or function} | {One line} |

## Sources

- {Primary text — title, author if known, date}
- {Secondary or web source — title and URL if applicable}
```

## Existing file handling

Before writing, check if the target file already exists. If it does:

1. Generate the new content in memory.
2. Show the user a unified diff between the existing file and the proposed new content.
3. Ask: "File already exists. Apply these changes? (y/n/skip)" — `y` overwrites, `n` keeps the old file, `skip` moves on without asking again for the rest of the batch.

Use `diff -u` or equivalent for the diff output. If the new content is identical to the existing file, skip silently and note it in the final summary.

## Web search policy

Use web search for:
- Etymology and native-language names when not obvious
- Trademark verification (search "{race name} trademark", check registered marks in major game/IP categories)
- Confirming source mythology when uncertain
- Notable game appearances when the skill's general knowledge is incomplete

When sources conflict (common with folklore — e.g. Slavic and Germanic both have "dwarf-like" beings with overlapping traits), do not pick one silently. Flag the ambiguity in the file under a `> **Note:**` callout and present the major variants.

For trademark info, always include the disclaimer that this is general guidance and not legal advice — the user should verify with the relevant trademark database before commercial use.

## Template

The exact output template is in `references/template.md`. Read that file and use it verbatim for every race file produced. Do not improvise sections or reorder fields.

## Final summary

After processing the batch, print a summary:

```
Created: 6 files
  - races/valkyrie_norse.md + races/valkyrie_norse_roster.md
  - races/dwarf_norse.md + races/dwarf_norse_roster.md
  - races/dwarf_slavic.md + races/dwarf_slavic_roster.md
Skipped: 1 (Thor — individual figure, user declined Æsir)
Updated: 2 (races/orc_modern_literary.md + races/orc_modern_literary_roster.md — user accepted diff)
Flags: 1 (dwarf_slavic.md — sources conflict on physical description, both noted)
```

## Examples

**Example 1 — single race:**
Input: `Valkyries`
→ Single Norse origin. Create `races/valkyrie_norse.md`.

**Example 2 — batch, multi-origin:**
Input: `Valkyries, Dwarves, Orcs`
→ `valkyrie_norse.md`, `dwarf_norse.md`, `dwarf_slavic.md`, `orc_modern_literary.md`. Confirm the dwarf split with the user before generating.

**Example 3 — individual figure:**
Input: `Thor`
→ Ask: "Thor is an individual figure, not a race. Did you mean Æsir (the race of Asgardian gods)? (y/n)". If yes, generate `aesir_norse.md`.

**Example 4 — unique being:**
Input: `Fenrir`
→ Ask: "Fenrir is a unique being, not a race. Closest race-like groupings: 'jötnar offspring' or 'monstrous wolves of Norse myth'. Pick one or skip." Generate the chosen one.

**Example 5 — newline batch:**
Input:
```
Kitsune
Tengu
Oni
```
→ `kitsune_east_asian.md`, `tengu_east_asian.md`, `oni_east_asian.md`.
