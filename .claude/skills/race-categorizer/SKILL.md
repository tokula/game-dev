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
4. **Research each variant** — before any web search, read `.claude/skills/race-categorizer/references/sources_registry.md`. For any source you plan to cite that already appears in the registry, use its registered `Tag` and `URL` directly — do not mark `*` or re-research its status. Sources listed under **Known blocked sources** in the registry are automatically excluded. Only run web searches for provenance of sources genuinely absent from the registry.

   Sources listed in the `## Blocked sources` section of the registry are excluded immediately without re-researching. Sources tagged `[CC-BY-NC*]` or `[CC-BY-NC-ND]` are research-only — use them to confirm facts but never cite them in race files; always attribute the fact to its PD primary source instead. After the registry check, use web search for general details, etymology, named individuals attested in primary sources, and trademark verification. Flag conflicting sources.

   **4b — Classify each trait** — for every trait discovered, walk this decision order (first match wins):
   1. **Specialization** — does it break normal physical/biological rules? No training or luck in another race could replicate it. Tag `[minor]`, `[major]`, or `[signature]` by impact.
   2. **Modifier** — is it an imposed condition (curse, blessing, oath, luck pattern)? Could a god or ritual lift it without changing what the species fundamentally is?
   3. **Fault** — is it an intrinsic vulnerability or limitation tied to the species' nature? Removing it would mean the creature isn't that race anymore.
   4. **Strength** — is it an intrinsic positive attribute within normal biological/cognitive range?
   5. **Characteristic** — neutral defining feature that is neither advantage nor limitation.

   Borderline rule: **biology → Fault; story/magic-imposed → Modifier; rule-breaking magnitude → Specialization.**

   Specific borderline cases:
   - "Garlic aversion" — intrinsic to the species (can't cure a vampire of it) → **Fault**. If a specific story frames it as a curse on one bloodline → **Modifier**.
   - "Fear of cats" — biological predator-recognition → **Fault**. Curse from a cat-deity → **Modifier**.
   - "Extreme jumping" — leaps further than most → **Strength**. Leaps over rooftops in a single bound → **Specialization**.
   - "Long-lived" — has tradeoffs (outliving loved ones, slow population growth) → **Characteristic** by default, unless the race treats it as purely advantageous.
   - "Telepathy" — their only communication mode → **Characteristic**. Can project thoughts into other species against their will → **Specialization**.

   **4c — Fill non-trait fields** — after trait classification, populate these three fields:

   **Gender(s):**
   - Capture both biological sex and social gender expression.
   - If they align, write one value (e.g. "Female"). If they differ, note both (e.g. "Biologically asexual; socially gendered female in primary sources").
   - Sexless, spirit, elemental, or construct races: write "Sexless / N/A" unless primary sources specify otherwise.
   - Game variants that introduce a different gender → Game depictions section only; do not alter this field.

   **Allies / Rivals / Enemies table:**
   - Source: primary sources or original canon only. If a relationship appears only in a game or novel, it goes in the Roster's Games table — not here.
   - Use exactly these five Standing values: **Ally** (cooperative, mutual benefit, roughly equal standing), **Superior** (has authority over this race), **Charge** (under this race's authority or protection — link their race file if it exists), **Rival** (competes for territory, role, or resource without necessarily fighting), **Enemy** (actively opposes or fights).
   - This section is fully separate from Modifiers. **Modifiers** captures the binding mechanism — the oath, the curse, the consequence of defiance. **Allies / Rivals / Enemies** captures faction standing. The same fact does NOT appear in both sections.
   - Cross-source conflicts: do not silently pick one. Flag in the Notes column: `"Enemy in most sources; Rival in [source] — see Sources note"`. Add a Sources note callout if needed.

   **Ecology:**
   - **Prey**: what this race consumes for food OR hunts for sport or pleasure (e.g. a Predator-style race that hunts other species as trophies). If neither applies, write `N/A — {reason}`.
   - **Predators**: what naturally hunts, threatens, or preys upon this race. Intentional overlap with Faults is acceptable — both perspectives are useful.
   - For divine, immortal, or spirit races with no food chain: `N/A — divine/immortal beings` for both fields.

5. **Present sources for approval** — before writing anything, list every source found for the variant and ask the user to confirm which to keep. Prefix each entry with its provenance tag. Sources that fail the IP filter are listed but marked `[BLOCKED]` and excluded automatically — the user cannot unblock them.

   Provenance tags: `[PD]` public domain · `[CC0]` Creative Commons Zero · `[CC-BY]` / `[CC-BY-SA]` CC Attribution · `[SRD]` D&D 5e SRD (CC-BY-4.0) · `[OGL]` Open Game License

   Append `*` to any tag when provenance is a best-guess rather than confirmed (e.g. `[PD*]` — text appears old, no copyright notice found, but not formally verified). Never omit the tag and never silently assume safety; a flagged guess is always better than false confidence. Proprietary sources (game wikis, paywalled sites) do not appear in this list at all — they are excluded by the IP filter before reaching this step.

   ```
   Sources found for Valkyrie (Norse):
     [1] [PD] Völuspá, Poetic Edda (c. 10th–13th century) — names six Valkyries, primary cosmological role
     [2] [PD] Grímnismál, Poetic Edda — lists thirteen named Valkyries
     [3] [PD] Gylfaginning, Prose Edda, Snorri Sturluson (c. 1220) — battlefield role description
     [4] [PD*] World History Encyclopedia: "Valkyrie" — secondary overview, licence not explicitly stated

   Use all? Or discard any? (enter numbers to discard, or press enter to use all)
   ```

   Only proceed to step 6 once the user has confirmed. Omit discarded sources from all file content.

   **5b — Update the registry** — for each approved source not already in `sources_registry.md`, append a new row to the appropriate tradition section (or Secondary/Academic if cross-tradition). Include title, author, date, tag (with `*` if uncertain), URL if found, and any notes. Do not add BLOCKED sources.

6. **Fill the template** for each variant. The template lives at `.claude/skills/race-categorizer/references/template.md` (relative to the project root) — read it and follow it exactly. It includes the **Gender(s)** header field, the **Allies / Rivals / Enemies** table, and the **Ecology** section (Prey + Predators) — populate all three using the rules in step 4c. Also prepare a companion `{name}_{source}_roster.md` (see "Roster file").
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

## IP filter

**Applies at two levels:**

- **Race level:** Reject any race whose only origin is proprietary IP (e.g. Mind Flayers, Smurfs, Warcraft-specific lore). If a public-domain analogue exists, suggest it; otherwise skip the entry and note the reason in the final summary.
- **Section level:** Omit the *Named individuals — Games* and *Game depictions* sections from any file unless the game source is public domain, CC0, or explicitly open-licensed (e.g. D&D 5e SRD / CC-BY-4.0). Mythology-origin races (Valkyries, Dwarves, etc.) are still valid — only their game-derivative sections are affected.

This applies regardless of how well-known the property is.

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
- Tieflings (D&D SRD) → `tiefling_modern_literary.md` *(open-licensed via D&D 5e SRD / CC-BY-4.0)*
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

**Source priority:** Primary sources (folklore, mythology, religious texts, historical accounts)
are the authoritative origin. Games and fiction derive from them. Always populate
`— Primary sources` first and most completely. Derivative sections exist for cross-reference
only — they should never displace or reframe the original cultural record.

**Section naming rule:** Use `— Primary sources` when the race has a real-world folkloric or
mythological base. Use `— Original canon` instead when the race has no such precedent and the
authoritative record is a specific creative work that is public domain, CC0, or explicitly
open-licensed (e.g. Tieflings → D&D 5e SRD / CC-BY-4.0). Proprietary-only races are blocked
entirely by the IP filter and never reach this label. The label signals whether you're citing
a cultural tradition or an open creative work.

## Named individuals — Primary sources / Original canon

**Origin layer.** For mythological/folkloric races: names attested in folklore, mythology,
saga, religious texts, or historical accounts — public domain, culturally authentic, preferred
for game design use. For original creations: names from the definitive source work (first
published appearance or authorial canon).

| Name | Source | Rank / role | Notes |
|------|--------|-------------|-------|
| {Name} | {Primary text, e.g. *Völuspá*} | {Rank or function} | {One line} |

## Individual distinctions — Primary sources / Original canon

Primary and original-canon individuals only. Game-invented figures have their own table and do not belong here. Only include individuals who deviate meaningfully from the racial template — traits the race profile does not already cover for all members. Use the same five-category tags from step 4b. Omit this section entirely if no individuals stand out from the baseline.

**{Name}**
- `[Specialization-major]` {trait that exceeds what the racial profile grants all members}
- `[Modifier]` {imposed condition unique to this individual} — *{origin}*
- `[Fault]` {vulnerability specific to this subtype or individual}

## Named individuals — Games

**Derivative.** Game-invented or game-adapted names, included for cross-reference only.
Do not treat these as equivalent to primary-source attestation.
**IP filter applies:** only include entries from public domain, CC0, or explicitly open-licensed sources (e.g. D&D 5e SRD / CC-BY-4.0). Omit this section entirely if no qualifying entries exist.

| Name | Game | Role | Notes |
|------|------|------|-------|
| {Name} | {Game title (studio)} | {Role in game} | {One line} |

## Named individuals — Modern literary

**Derivative.** Names from novels, comics, or other non-game fiction, included for
cross-reference only.

| Name | Work | Role | Notes |
|------|------|------|-------|
| {Name} | {Title, author} | {Role} | {One line} |

Omit any section that has no entries.

## Sources

### Primary sources

- {Folklore, mythology, saga, religious text, or historical account — title, author if known, date}

### Secondary sources

- {Encyclopedia entry, academic overview, or web source — title and URL if applicable}
```

## Existing file handling

Before writing, check if the target file already exists. If it does:

1. Generate the new content in memory.
2. Show the user a unified diff between the existing file and the proposed new content.
3. Ask: "File already exists. Apply these changes? (y/n/skip)" — `y` overwrites, `n` keeps the old file, `skip` moves on without asking again for the rest of the batch.

Use `git diff --no-index --word-diff` via PowerShell for the diff output (Bash has no access to Windows paths; `diff -u` will not work). Exit code 1 from `git diff --no-index` means files differ — it is not an error. If the new content is identical to the existing file, skip silently and note it in the final summary.

## Web search policy

Use web search for:
- Etymology and native-language names when not obvious
- Trademark verification (search "{race name} trademark", check registered marks in major game/IP categories)
- Copyright/public domain status of the key source text(s): check the publication date and author (for post-1700 works: author's death year + 70 years in most jurisdictions). Also search "{race name} game adaptation" or "{source title} game" to identify notable modern derivatives that carry their own IP protection — list these under "Protected derivatives" in the template.
- Confirming source mythology when uncertain

When sources conflict (common with folklore — e.g. Slavic and Germanic both have "dwarf-like" beings with overlapping traits), do not pick one silently. Flag the ambiguity in the file under a `> **Note:**` callout and present the major variants.

For trademark info, always include the disclaimer that this is general guidance and not legal advice — the user should verify with the relevant trademark database before commercial use.

## Registry management

The registry at `.claude/skills/race-categorizer/references/sources_registry.md` is the single source of truth for provenance. It is consulted automatically in step 4 and updated automatically in step 5b. Three explicit commands are also available:

**`bootstrap registry`** — re-sync the registry from existing race files (run after adding many files outside the skill):
1. Scan all `.md` files under `races/` (skip `_roster.md` files)
2. Extract every entry from `### Primary sources` and `### Secondary sources` sections
3. Deduplicate by title; for each entry not already in the registry, propose adding it
4. Present proposed additions for user review
5. Append approved entries to `sources_registry.md`

**`audit races`** — find and fix missing or inconsistent tags across all race files:
1. Read `sources_registry.md`
2. Scan all race profile files (skip rosters) for source entries missing a tag or whose tag differs from the registry
3. Show a summary: count of files affected and nature of changes
4. For each affected file, show a `git diff --no-index --word-diff` and ask `apply? (y/n/skip)`
5. Write approved updates; report final counts (updated / skipped / unchanged)

**`confirm [source title]`** — promote a `[TAG*]` entry to confirmed:
1. Find the matching row in `sources_registry.md` by title (partial match is fine if unambiguous)
2. Remove the `*` from the tag
3. Offer to run `audit races` immediately to back-propagate the confirmed tag to all race files that cite this source

## Template

The exact output template is in `.claude/skills/race-categorizer/references/template.md` (relative to the project root). Read that file and use it verbatim for every race file produced. Do not improvise sections or reorder fields.

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
