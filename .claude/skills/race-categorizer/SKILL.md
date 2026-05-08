---
name: race-categorizer
description: Categorize fictional or mythological races/species used in games (such as Valkyries, Orcs, Leprechauns, Dwarves, Vampires, Tieflings) into structured Markdown profile files following a fixed template. Use this skill whenever the user wants to research, document, classify, or build a reference library of game races, mythological beings, fantasy species, monster types, or playable races, even if they don't explicitly say "categorize" — phrases like "make a race profile for X", "add Orcs to my races folder", "look up Valkyries", or "what kind of creature is a Kitsune" should all trigger this skill. Also trigger for batch requests like "Valkyries, Orcs, Leprechauns" and for races with multiple cultural origins (e.g. Dwarves exist in both Norse and Slavic myth — produce one file per origin). Also trigger for named divine groups, divine collectives, or finite groups of legendary beings (e.g. "Three Sovereigns", "Nine Muses", "Four Heavenly Kings").
---

# Race Categorizer

Build structured Markdown profiles of mythological and fictional races for game-design reference. Each profile follows a fixed template and is saved as a `.md` file in a races library folder.

## Workflow

1. **Parse input** — accept a single name, comma-separated list, or newline-separated list. Treat each entry independently.
2. **Validate each entry** — distinguish races from individual figures and from unique beings (see "Input validation").
3. **For each valid race entry, identify source variants** — if the race appears in multiple distinct mythologies (e.g. Dwarves in Norse vs. Slavic), produce one file per source. See "Multiple source mythologies".
4. **Research each variant** — before any web search, read `.claude/skills/race-categorizer/references/sources_registry.md`. For any source you plan to cite that already appears in the registry, use its registered `Tag` and `URL` directly — do not mark `*` or re-research its status. Only run web searches for provenance of sources genuinely absent from the registry.

   **4a — Extract blocked domains first (mandatory, before any fetch or subagent call).** Read the `## Blocked sources` table from `sources_registry.md` and build a domain blocklist. Do not visit, fetch, or search any URL whose domain matches a blocked entry — even if a search result links to it. When delegating research to a subagent, copy the full blocklist into the subagent prompt explicitly so it cannot visit blocked domains either. This step must happen before spawning any agent or issuing any web call.

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
   - Named Collectives with mixed-gender members: write "Varied — see individual roster entries"; populate per-member gender in the roster's Individual distinctions section.

   **Allies / Rivals / Enemies table:**
   - Source: primary sources or original canon only. If a relationship appears only in a game or novel, it goes in the Roster's Games table — not here.
   - Use exactly these five Standing values: **Ally** (cooperative, mutual benefit, roughly equal standing), **Superior** (has authority over this race), **Charge** (under this race's authority or protection — link their race file if it exists), **Rival** (competes for territory, role, or resource without necessarily fighting), **Enemy** (actively opposes or fights).
   - This section is fully separate from Modifiers. **Modifiers** captures the binding mechanism — the oath, the curse, the consequence of defiance. **Allies / Rivals / Enemies** captures faction standing. The same fact does NOT appear in both sections.
   - Cross-source conflicts: do not silently pick one. Flag in the Notes column: `"Enemy in most sources; Rival in [source] — see Sources note"`. Add a Sources note callout if needed.

   **Ecology:**
   - **Prey**: what this race consumes for food OR hunts for sport or pleasure (e.g. a Predator-style race that hunts other species as trophies). If neither applies, write `N/A — {reason}`.
   - **Predators**: what naturally hunts, threatens, or preys upon this race. Intentional overlap with Faults is acceptable — both perspectives are useful.
   - For divine, immortal, or spirit races with no food chain: `N/A — divine/immortal beings` for both fields.

   **Internal relationships (Named Collective only):**
   - Source: primary sources only. Omit if fewer than two named inter-member bonds are attested.
   - Use exactly these five Standing values: **Paired**, **Serves**, **Succession**, **Conflict**, **Binds** — defined in the template.
   - This section is distinct from Allies / Rivals / Enemies (which covers external factions). The same fact does NOT appear in both.
   - If a member's relationship to the collective is already fully described by their Individual distinctions in the roster, do not duplicate it here — only include relationships that are meaningfully interactive (not just hierarchical position).

   **Mortal interaction (Named Collective only):**
   - Source: primary sources only. Only include modes where a specific context, timing, or ritual form is attested — not generic reverence.
   - "Members involved" field: name the specific member(s) primarily addressed or invoked in that mode. Write "All" only if the collective is genuinely invoked as a unit.
   - Omit the section entirely if no specific invocation or ritual context is attested in primary sources.

   **Encounter conditions (Race and Unique creature — optional):**
   - Include only when primary sources describe rules specific enough to function as game mechanics: a trigger (what initiates contact), a protocol (what governs the exchange), and a consequence (what happens on compliance or violation).
   - General traits (e.g. "they attack intruders") belong in Specializations or Faults, not here. This section is for structured interaction rules, not behavioral tendencies.
   - Omit entirely when no such rules are attested. Do not add this section by default.

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

   **Unique creature template delta:** Two differences from the race template: (a) **include** the `## Physical description` section (attested appearance from primary sources; flag conflicting descriptions with a `> **Note:**` callout); (b) **omit** the `Common archetype/role in fiction` header field. All other sections apply unchanged.

   **Named Collective template delta:** Five differences from the regular race template:
   (a) Set `Entity type` to "Named Collective".
   (b) Populate the new `Membership:` field — count, how membership is determined, whether it varies by source.
   (c) In the roster file, the `## Individual distinctions` section is **mandatory and comprehensive** — list all named members, even those who closely resemble the collective profile. For Named Collectives, individual variation is the primary game-design payload. Do not omit this section.
   (d) Add `## Internal relationships` section — populate using the Standing values and guidance in step 4c. Omit if fewer than two named inter-member relationships are attested.
   (e) Add `## Mortal interaction` section — populate using the guidance in step 4c. Omit if no specific invocation or ritual context is attested in primary sources.
   All other race template sections apply unchanged.

   **Race and Unique creature template — optional section:** `## Encounter conditions` — include when primary sources describe specific trigger conditions, social protocols, or interaction rules governing mortal contact with this race or creature. Use the table structure from the template (Mode / Trigger / Protocol / Source). Omit entirely when no such rules are attested; do not add the section by default.

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

**(c) Unique being with no parent race** — two sub-types:

**(c1) Monstrous unique beings** — no race, no divine domain. Two paths depending on whether a plausible racial grouping exists:
- If a grouping exists, offer both options inline: "There's no race for [X]. Closest groupings: [A], [B]. Use one of these, or create a creature profile in `creatures/` instead? (pick grouping / creature / skip)"
- If no plausible grouping exists, route directly to creatures: "There's no race for [X] — [X] appears to be a unique being with no racial grouping. Create a creature profile in `creatures/`? (y/n)"
- Fenrir → plausible groupings: "jötnar offspring", "monstrous wolves of Norse myth" — or creature profile
- Jörmungandr → plausible groupings: "jötnar offspring", "primordial serpents" — or creature profile
- Cerberus → plausible groupings: "monstrous hounds of Greek underworld" — or creature profile
- Kui → no plausible grouping → creature profile

**(c2) Singular deities and cosmological personifications** — ontologically unique beings with no parent race and no meaningful racial grouping. Distinguish three grades:
- **Singular Deity** — a named divine individual who governs a domain and can act, be offended, or be appeased (e.g. Peckols, Veliona, Zemes Māte)
- **Cosmological Personification** — a being that *is* a concept rather than inhabiting it; impersonal, non-interactable as a person (e.g. Giltinė, Thanatos, Ananke, Kala)
- **Primordial Being** — predates or constitutes the cosmic order; may be either personal or impersonal (e.g. God in Abrahamic theology)

  > **Distinguishing Cosmological Personification from Unique creature:** If the entity has a separable physical form or tangible presence described in primary sources — a body, a location, the possibility of a physical encounter — route to `creatures/` as a Unique creature instead. Only use Cosmological Personification when the being IS the concept: no body, no fixed location, no physical encounter possible. Example: "The Unanswered Prayer" → if primary sources describe it as a tangible entity that haunts a place, it's a Unique creature; if it is simply the abstract condition of prayers going unanswered, personified without form, it's a Cosmological Personification.

Ask inline: "There's no race for [X] — [X] is a [Singular Deity / Cosmological Personification / Primordial Being]. Create a deity profile in `deities/` instead? (y/n)". If yes, proceed with the same template (set `Entity type` accordingly) but skip the roster file. If no, skip.

If the user is unsure or the input is ambiguous, ask before proceeding rather than guessing.

**(d) Named Collective** — a finite, named group of divine or legendary beings who share a common role, cosmic status, or cultural tradition, but are not a reproductive biological species. The group is defined by its named membership list (which may vary across sources) rather than by shared biology. Route to `named-collectives/` with Entity type "Named Collective". A roster file is always required.

Recognising a Named Collective:
- A small, enumerable set of named individuals (typically 2–12)
- Membership determined by role, lineage, divine selection, or tradition — not by reproduction
- The group functions as a unified game-design type (each member is an instance of that type)
- Examples: Three Sovereigns 三皇 (divine culture-creators, Chinese), Five Emperors 五帝 (sage rulers, Chinese), the Nine Muses (divine inspirers, Greek), the Four Heavenly Kings (guardians, Chinese Buddhist)

Ask inline: "[X] is a Named Collective — a finite group of [role] beings rather than a species. Create a Named Collective profile in `named-collectives/`? (y/n)". If yes, proceed with Entity type "Named Collective". If the user prefers individual deity profiles instead, route each member through category (c2).

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
- Valkyries → `races/valkyrie_norse.md`
- Tieflings (D&D SRD) → `races/tiefling_modern_literary.md` *(open-licensed via D&D 5e SRD / CC-BY-4.0)*
- Kitsune → `races/kitsune_east_asian.md`
- Giltinė (deity profile) → `deities/giltine_baltic.md`
- Thanatos (deity profile) → `deities/thanatos_greek.md`
- Kui (creature profile) → `creatures/kui_east_asian.md`

## Output folder

Default: `./races/` in the current working directory. Create it if it doesn't exist. If the user specifies a different folder, use that instead.

**Deity profiles** (`Entity type: Singular Deity`, `Cosmological Personification`, or `Primordial Being`) go in `./deities/` instead. Create it if it doesn't exist.

**Creature profiles** (`Entity type: Unique creature`) go in `./creatures/` instead. Create it if it doesn't exist.

**Named Collective profiles** (`Entity type: Named Collective`) go in `./named-collectives/` instead. Create it if it doesn't exist.

## Roster file

Every race profile gets a companion `{name}_{source}_roster.md` written alongside it. Cross-link both files with a `> See also:` line at the top of each.

**Deity profiles** (`Entity type: Singular Deity`, `Cosmological Personification`, or `Primordial Being`) are singular by definition — skip the roster file entirely.

**Creature profiles** (`Entity type: Unique creature`) are singular by definition — skip the roster file entirely.

**Filename:** same base as the race file with `_roster` appended — e.g. `valkyrie_norse_roster.md`, `dwarf_norse_roster.md`.

**Structure:**

The exact roster template is in `.claude/skills/race-categorizer/references/roster_template.md` — read it and follow it verbatim for every roster file produced. Do not improvise sections or reorder fields.

## Existing file handling

Before writing, check if the target file already exists. If it does:

1. Generate the new content in memory.
2. Show the user a unified diff between the existing file and the proposed new content.
3. Ask: "File already exists. Apply these changes? (y/n/skip)" — `y` overwrites, `n` keeps the old file, `skip` moves on without asking again for the rest of the batch.

Use `git diff --no-index --word-diff` via PowerShell for the diff output (Bash has no access to Windows paths; `diff -u` will not work). Exit code 1 from `git diff --no-index` means files differ — it is not an error. If the new content is identical to the existing file, skip silently and note it in the final summary.

## Online operations policy

**Online budget:** Every call to `WebSearch` or `WebFetch` — regardless of who makes it (main session or a spawned subagent) — counts as **1 operation** against a limit of **3 per race or deity variant**. There is no separate budget for fetches vs. searches; they draw from the same pool. After 3 operations total, present what was found and ask: "Used 3 online operations for [Name]. Use up to 3 more? (y/n)". If the user declines, proceed with available data and note any gaps in the file.

**Blocked domains apply to all operations.** The domain blocklist built in step 4a governs every online action without exception:
- `WebSearch`: pass the blocklist as `blocked_domains` parameter; never follow a search result link whose domain is blocked.
- `WebFetch`: before calling, check the target URL's domain against the blocklist. If it matches, skip the fetch and note the gap.
- Subagent delegation: when spawning an agent to do research, pass (a) the full domain blocklist and (b) the remaining operation count explicitly in the prompt. The subagent's tool calls count against the parent's budget — track the total across all agents.

**CC-BY-NC content** retrieved via any operation (search result or fetched page) is research-only — never cite it or reproduce its expression in output files; always attribute the underlying fact to its PD primary source.

Use online operations for:
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

## Templates

Two template files govern all output — read both before writing any files:

- **Race / Named Collective / deity / creature profile:** `.claude/skills/race-categorizer/references/template.md` — use verbatim for every main profile file.
- **Roster file:** `.claude/skills/race-categorizer/references/roster_template.md` — use verbatim for every companion roster file (races and Named Collectives; skip for deities and creatures).

Do not improvise sections or reorder fields in either template.

## Final summary

After processing the batch, print a summary:

```
Created: 7 files
  - races/valkyrie_norse.md + races/valkyrie_norse_roster.md
  - races/dwarf_norse.md + races/dwarf_norse_roster.md
  - races/dwarf_slavic.md + races/dwarf_slavic_roster.md
  - creatures/kui_east_asian.md
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

**Example 6 — Named Collective:**
Input: `Three Sovereigns`
→ Ask: "The Three Sovereigns (三皇) are a Named Collective — a finite group of divine culture-creators rather than a species. Create a Named Collective profile in `named-collectives/`? (y/n)". If yes, generate `named-collectives/san_huang_east_asian.md` + `named-collectives/san_huang_east_asian_roster.md`.
