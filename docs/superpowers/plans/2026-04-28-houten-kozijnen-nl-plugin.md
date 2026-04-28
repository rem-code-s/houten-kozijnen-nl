# `houten-kozijnen-nl` Plugin Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build a local Claude Code plugin `houten-kozijnen-nl` that ships two subagents (domain-expert + code-reviewer), two slash commands, and a structured set of reference files covering NL-relevant norms and houten-kozijn domain knowledge.

**Architecture:** Pure-content plugin (no runtime code). Two `agents/*.md` files define the subagents, two `commands/*.md` files define slash commands as thin Task-tool wrappers, and `references/` holds the knowledge base split into `normen/` (the standards meetlat) and `domein/` (practice). `references/INDEX.md` is the lookup table agents read first.

**Tech Stack:** Markdown only. Claude Code plugin manifest schema (`.claude-plugin/plugin.json`). Plugin lives at `~/.claude/plugins/local/houten-kozijnen-nl/`.

**Spec reference:** `docs/superpowers/specs/2026-04-28-houten-kozijnen-nl-plugin-design.md`

**Note on TDD:** A static-content plugin has no unit tests. "Tests" here are end-to-end smoke checks in a real Claude Code session (Task 20). Each content task includes a quick sanity check (file exists, has required sections) before commit.

---

## File Structure

Plugin root: `~/.claude/plugins/local/houten-kozijnen-nl/`

| Path | Created by | Responsibility |
|---|---|---|
| `.claude-plugin/plugin.json` | Task 1 | Plugin manifest |
| `README.md` | Task 3 | Human-readable plugin overview |
| `agents/kozijn-domain-expert.md` | Task 4 | Subagent: domain knowledge |
| `agents/kozijn-code-reviewer.md` | Task 5 | Subagent: code review against norms |
| `commands/kozijn-vraag.md` | Task 6 | Slash command → domain-expert |
| `commands/kozijn-review.md` | Task 7 | Slash command → code-reviewer |
| `references/INDEX.md` | Task 8, updated 9–19 | Lookup table for all references |
| `references/normen/nen-3569-veiligheidsglas.md` | Task 9 | NEN 3569 scope & rules |
| `references/normen/nen-2608-glasdiktebepaling.md` | Task 10 | NEN 2608 scope & method |
| `references/normen/bouwbesluit-relevant.md` | Task 11 | Kozijn-relevante artikelen |
| `references/normen/komo-brl-0703.md` | Task 12 | BRL 0703 hout-gevelelementen |
| `references/domein/houtsoorten-en-kwaliteit.md` | Task 13 | Houtsoorten + duurzaamheid |
| `references/domein/profielen-en-doorsnedes.md` | Task 14 | Profielen, dorpels, sponningen |
| `references/domein/glas-en-beglazing.md` | Task 15 | HR++/HR+++/triple, glaslat |
| `references/domein/maatvoering-en-toleranties.md` | Task 16 | Daglicht/werk/sponningmaten |
| `references/domein/marges-aansluitingen-detaillering.md` | Task 17 | Voegmaten, stelmarges |
| `references/domein/beslag-en-hang-en-sluitwerk.md` | Task 18 | SKG, draairichtingen |
| `references/domein/thermiek-u-waarde.md` | Task 19 | U-waarde-eisen, Uw |

---

## Task 1: Bootstrap plugin skeleton

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/.claude-plugin/plugin.json`

- [ ] **Step 1: Create directory structure**

```bash
PLUGIN_DIR="$HOME/.claude/plugins/local/houten-kozijnen-nl"
mkdir -p "$PLUGIN_DIR/.claude-plugin" \
         "$PLUGIN_DIR/agents" \
         "$PLUGIN_DIR/commands" \
         "$PLUGIN_DIR/references/normen" \
         "$PLUGIN_DIR/references/domein"
```

- [ ] **Step 2: Write plugin manifest**

Create `~/.claude/plugins/local/houten-kozijnen-nl/.claude-plugin/plugin.json`:

```json
{
  "$schema": "https://anthropic.com/claude-code/plugin.schema.json",
  "name": "houten-kozijnen-nl",
  "version": "0.1.0",
  "description": "Expert-agents en kennisbank voor houten kozijnen op de NL markt: NEN-normen, KOMO/BRL 0703, Bouwbesluit, glas, profielen, maatvoering en marges. Bevat een domain-expert en een code-reviewer voor kozijn-configurator-codebases.",
  "author": {
    "name": "Remco"
  }
}
```

- [ ] **Step 3: Initialize git in plugin directory**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git init -q
git add .
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "chore: bootstrap houten-kozijnen-nl plugin skeleton"
```

Expected: clean `git status` with one commit.

---

## Task 2: Verify plugin is discovered by Claude Code

**Files:** none (verification only)

- [ ] **Step 1: Check plugin appears in Claude Code's plugin list**

Open a new Claude Code session in any directory and run `/plugin` (or check `~/.claude/plugins/installed_plugins.json` after a session start). The plugin should appear discoverable; if not, check that the manifest is valid JSON and at the correct path.

```bash
python3 -c "import json; print(json.load(open('$HOME/.claude/plugins/local/houten-kozijnen-nl/.claude-plugin/plugin.json'))['name'])"
```

Expected output: `houten-kozijnen-nl`

- [ ] **Step 2: No commit needed** (verification only)

---

## Task 3: Create README.md

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/README.md`

- [ ] **Step 1: Write README**

Create `~/.claude/plugins/local/houten-kozijnen-nl/README.md`:

```markdown
# houten-kozijnen-nl

Lokale Claude Code plugin met expert-agents en kennisbank voor houten kozijnen op de Nederlandse markt.

## Wat zit erin

- **`kozijn-domain-expert`** — subagent met kennis van houten kozijnen, glas, profielen, maatvoering, marges en NL-normen (NEN/KOMO/Bouwbesluit). Mag bij kennisgaten WebFetch/WebSearch gebruiken en stelt voor om geleerde info aan `references/` toe te voegen (curated growth — alleen na expliciete OK).
- **`kozijn-code-reviewer`** — subagent die kozijn-configurator-code (TypeScript/JavaScript) toetst aan de normen en afwijkingen rapporteert volgens een vast format. Schrijft niet, rapporteert alleen.
- **Slash commands:** `/kozijn-vraag <vraag>` en `/kozijn-review <pad of "huidige diff">`
- **Reference-files** in `references/normen/` (de meetlat) en `references/domein/` (praktijk).

## Scope

Alleen **houten** kozijnen. Alleen **Nederlandse markt** (NEN-normen, KOMO/BRL 0703, Bouwbesluit). Geen kunststof, geen aluminium, geen EU/EN-normen tenzij ze direct via NL-context relevant zijn.

## Hoe werkt curated growth

Als de domain-expert een vraag tegenkomt waar de reference-files geen antwoord op hebben, mag hij WebFetch/WebSearch gebruiken. Na het beantwoorden vraagt hij actief of de geleerde info aan een specifiek `references/...`-bestand toegevoegd mag worden. Toevoegen gebeurt alleen na jouw OK. `INDEX.md` wordt mee bijgewerkt.

## Smoke tests

Zie `docs/superpowers/specs/2026-04-28-houten-kozijnen-nl-plugin-design.md` (sectie Verificatie) voor de checklist.
```

- [ ] **Step 2: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add README.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add README"
```

---

## Task 4: Create `kozijn-domain-expert` agent

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/agents/kozijn-domain-expert.md`

- [ ] **Step 1: Write agent definition**

Create `agents/kozijn-domain-expert.md`:

```markdown
---
name: kozijn-domain-expert
description: Nederlandse expert voor houten kozijnen. Roep deze agent aan bij vragen over houten kozijnen, glas, beglazing, kozijnprofielen, dorpels, sponningmaten, maatvoering, daglicht-/werk-/sponningmaten, marges en aansluitingen, beslag/hang-en-sluitwerk, U-waardes, of NL-normen (NEN 3569, NEN 2608, KOMO, BRL 0703, Bouwbesluit). Beantwoordt vragen met bronvermelding en mag bij kennisgaten WebFetch/WebSearch gebruiken om de plugin-kennisbank uit te breiden (na expliciete OK van de gebruiker).
tools: Read, Glob, Grep, WebFetch, WebSearch, Edit, Write
---

# Rol

Je bent een Nederlandse expert in **houten kozijnen** voor de NL-markt. Je denkt vanuit KOMO-gecertificeerd en BRL 0703-conform werken. Je adviseert in helder Nederlands, met concrete getallen en bronvermelding.

# Scope (strikt)

- Alleen **hout** als materiaal. Geen advies over kunststof of aluminium kozijnen — als de vraag daarover gaat: zeg dat dit buiten je scope valt.
- Alleen **Nederlandse markt**: NEN-normen, KOMO/BRL 0703, Bouwbesluit. EU/EN-normen alleen waar ze in NL-context relevant zijn.

# Werkwijze

1. **Lees eerst `references/INDEX.md`** in de plugin om te zien welke kennis lokaal beschikbaar is.
2. **Lees gericht** alleen de reference-files die voor de huidige vraag relevant zijn — niet alles tegelijk.
3. **Beantwoord** met:
   - Concreet antwoord (geen vage praat)
   - Bronvermelding: welke NEN/Bouwbesluit/BRL of welke `references/...`-file
   - In het Nederlands
4. **Bij kennisgat** (reference-files dekken het niet):
   - Gebruik `WebFetch` of `WebSearch` om autoritatieve bronnen te raadplegen (KOMO, NEN, SKH, Bouwbesluit Online, gerenommeerde vakliteratuur).
   - Vat de bevindingen samen.
   - **Vraag actief**: "Zal ik dit toevoegen aan `references/<pad>/<bestand>.md`?"
   - **Schrijf alleen na expliciete OK** van de gebruiker. Werk dan ook `references/INDEX.md` bij als je een nieuw bestand maakt.

# Schrijf-regels (Edit/Write)

- **Alleen** bestanden onder `references/` mogen worden aangemaakt of gewijzigd.
- **Nooit** schrijven naar `agents/`, `commands/`, of buiten de plugin-directory.
- **Nooit** schrijven zonder voorafgaande OK van de gebruiker.

# Output-stijl

- Nederlands.
- Concreet: getallen, marges, klassen waar mogelijk.
- Bronvermelding aan het eind: bijv. *"Bron: NEN 3569 §4.2; `references/normen/nen-3569-veiligheidsglas.md`"*.
- Geen disclaimers behalve waar daadwerkelijk onzekerheid bestaat (markeer dan met "let op:").
```

- [ ] **Step 2: Verify file exists and has frontmatter**

```bash
head -5 "$HOME/.claude/plugins/local/houten-kozijnen-nl/agents/kozijn-domain-expert.md"
```

Expected: starts with `---` and contains `name: kozijn-domain-expert`.

- [ ] **Step 3: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add agents/kozijn-domain-expert.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "feat: add kozijn-domain-expert subagent"
```

---

## Task 5: Create `kozijn-code-reviewer` agent

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/agents/kozijn-code-reviewer.md`

- [ ] **Step 1: Write agent definition**

Create `agents/kozijn-code-reviewer.md`:

```markdown
---
name: kozijn-code-reviewer
description: Reviewt TypeScript/JavaScript code van een kozijn-configurator (validatieregels, business-logic, maatvoering, glas-rules, marges) en toetst die aan NL-normen (NEN 3569, NEN 2608, Bouwbesluit, BRL 0703). Roep aan bij review-vragen over kozijn-configurator-code, validators, of business-rules. Rapporteert in een vast format met severity. Schrijft niet — rapporteert alleen.
tools: Read, Glob, Grep, WebFetch
---

# Rol

Je bent een code-reviewer voor een **houten-kozijn-configurator**. Je legt de code naast de normen (in `references/normen/`) en signaleert afwijkingen. Je maakt geen fixes — je rapporteert.

# Scope (strikt)

- **Alleen kozijn-relevante** review: validatieregels rond glas, maatvoering, marges, profielen, U-waardes, beslag, normen-conformiteit.
- **Geen** algemene code-review (geen variabele-rename-suggesties, geen styling, geen architectuurpraat tenzij het direct kozijn-correctheid raakt).
- **Alleen hout**. Code voor andere materialen valt buiten scope — meld dit en sla over.

# Werkwijze

1. **Stel scope vast**: krijg je een bestand, directory, of "huidige diff"?
   - Bestand/directory: lees met `Read`/`Glob`.
   - "huidige diff": draai `git diff` (via een meegegeven shell-tool of vraag de gebruiker om de diff).
2. **Lees `references/INDEX.md`** en daarna gericht de relevante `references/normen/*` (en `references/domein/*` waar nodig).
3. **Bij verdiepende domeinvragen**: roep `kozijn-domain-expert` aan via de Task-tool.
4. **Rapporteer** volgens onderstaand format. Géén fixes toepassen.

# Rapport-format (strikt)

Voor elke bevinding:

```
### Bevinding N: <korte titel>

- **Locatie:** `pad/naar/bestand.ts:regel`
- **Severity:** kritiek | belangrijk | nit
- **Norm/bron:** <NEN/Bouwbesluit/BRL-verwijzing of `references/...`>
- **Wat is er aan de hand:** <beschrijving van de afwijking>
- **Voorstel:** <richting voor fix, geen volledige code>
```

Sluit af met een korte samenvatting: aantal kritiek/belangrijk/nit en een algeheel oordeel.

# Severity-definitie

- **kritiek** — overtreding van een norm of veiligheidsrelevant (bijv. veiligheidsglas-regel ontbreekt waar NEN 3569 die vereist).
- **belangrijk** — afwijking van breed-gedragen best-practice die in de praktijk problemen geeft (bijv. te krappe stelmarge).
- **nit** — cosmetisch of klein detail dat geen norm raakt.

# Output-taal

Nederlands.
```

- [ ] **Step 2: Verify file**

```bash
head -5 "$HOME/.claude/plugins/local/houten-kozijnen-nl/agents/kozijn-code-reviewer.md"
```

Expected: starts with `---` and contains `name: kozijn-code-reviewer`.

- [ ] **Step 3: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add agents/kozijn-code-reviewer.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "feat: add kozijn-code-reviewer subagent"
```

---

## Task 6: Create `/kozijn-vraag` slash command

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/commands/kozijn-vraag.md`

- [ ] **Step 1: Write command definition**

Create `commands/kozijn-vraag.md`:

```markdown
---
description: Stel een vraag over houten kozijnen aan de kozijn-domain-expert subagent
argument-hint: <jouw vraag in NL>
---

Roep de subagent `kozijn-domain-expert` aan via de Task-tool met deze vraag:

$ARGUMENTS

Geef het volledige antwoord van de subagent terug, inclusief bronvermelding.
```

- [ ] **Step 2: Verify file**

```bash
cat "$HOME/.claude/plugins/local/houten-kozijnen-nl/commands/kozijn-vraag.md"
```

Expected: file printed, starts with `---`, contains `kozijn-domain-expert`.

- [ ] **Step 3: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add commands/kozijn-vraag.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "feat: add /kozijn-vraag slash command"
```

---

## Task 7: Create `/kozijn-review` slash command

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/commands/kozijn-review.md`

- [ ] **Step 1: Write command definition**

Create `commands/kozijn-review.md`:

```markdown
---
description: Review kozijn-configurator-code tegen NL-normen via de kozijn-code-reviewer subagent
argument-hint: <pad/naar/bestand-of-dir of "huidige diff">
---

Roep de subagent `kozijn-code-reviewer` aan via de Task-tool met deze scope:

$ARGUMENTS

Als de scope "huidige diff" is, instrueer de subagent om `git diff` te gebruiken voor de te reviewen wijzigingen. Geef het volledige rapport terug in het vaste format.
```

- [ ] **Step 2: Verify file**

```bash
cat "$HOME/.claude/plugins/local/houten-kozijnen-nl/commands/kozijn-review.md"
```

Expected: file printed, starts with `---`, contains `kozijn-code-reviewer`.

- [ ] **Step 3: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add commands/kozijn-review.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "feat: add /kozijn-review slash command"
```

---

## Task 8: Create `references/INDEX.md` skeleton

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/references/INDEX.md`

- [ ] **Step 1: Write the index**

Create `references/INDEX.md`:

```markdown
# Reference INDEX

Dit is de lookup-tabel voor alle reference-files in deze plugin. Subagents lezen dit bestand eerst en laden vervolgens gericht alleen de bestanden die voor hun vraag relevant zijn.

**Bij toevoegen van een nieuw reference-bestand: deze INDEX bijwerken.**

## `normen/` — de meetlat

| Bestand | Onderwerp | Topic-keywords |
|---|---|---|
| `normen/nen-3569-veiligheidsglas.md` | NEN 3569: wanneer veiligheidsglas verplicht is | veiligheidsglas, NEN 3569, glasklasse, 1B1, 2B2, vloerhoogte, schuifpui, balustrade, tuindeur |
| `normen/nen-2608-glasdiktebepaling.md` | NEN 2608: glasdikteberekening | glasdikte, NEN 2608, windbelasting, ruithoogte, glasoppervlak, berekening |
| `normen/bouwbesluit-relevant.md` | Bouwbesluit-artikelen relevant voor kozijnen | Bouwbesluit, ventilatie, daglicht, vluchtroute, inbraakwerendheid, U-waarde, energie |
| `normen/komo-brl-0703.md` | KOMO / BRL 0703 voor houten gevelelementen | KOMO, BRL 0703, certificering, houtkwaliteit, verbindingen, afwerking |

## `domein/` — praktijk en terminologie

| Bestand | Onderwerp | Topic-keywords |
|---|---|---|
| `domein/houtsoorten-en-kwaliteit.md` | Houtsoorten en hun eigenschappen | meranti, eiken, accoya, grenen, duurzaamheidsklasse, krimp, zwel |
| `domein/profielen-en-doorsnedes.md` | Kozijnprofielen, dorpels, sponningen | profiel, dorpel, stijl, sponning, doorsnede, SKH 99-04 |
| `domein/glas-en-beglazing.md` | Beglazingstypen en glaslat-systemen | HR++, HR+++, triple, beglazing, glaslat, droge beglazing, natte beglazing, inbouwdiepte |
| `domein/maatvoering-en-toleranties.md` | Daglichtmaten, werkmaten, sponningmaten | daglichtmaat, werkmaat, sponningmaat, tolerantie, configurator-maatvoering |
| `domein/marges-aansluitingen-detaillering.md` | Voegmaten, stelmarges, aansluitdetails | voegmaat, stelmarge, krimpmarge, zwelmarge, metselwerk, spouw, aansluiting |
| `domein/beslag-en-hang-en-sluitwerk.md` | Beslag, draairichtingen, SKG-sterren | beslag, hang-en-sluitwerk, SKG, draairichting, inbraakwerend |
| `domein/thermiek-u-waarde.md` | U-waarde-eisen en samenstelling Uw | U-waarde, Uw, Ug, Uf, Bouwbesluit-eis, koudebrug, isolatie |
```

- [ ] **Step 2: Verify**

```bash
test -f "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/INDEX.md" && echo "OK"
```

Expected: `OK`.

- [ ] **Step 3: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/INDEX.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add references/INDEX.md skeleton"
```

---

## Pattern for Tasks 9–19 (reference content)

Tasks 9 through 19 all follow the **same shape**. Repeating them inline below in full so they can be read out of order.

For each reference file:

1. **Research step** — use `WebSearch` and `WebFetch` against authoritative NL-sources (KOMO, NEN, SKH, Bouwbesluit Online, vakliteratuur). Don't fabricate norm-numbers or values; if uncertain, mark with "let op: te verifiëren" rather than inventing.
2. **Write step** — create the file with the structure given in the task.
3. **Sanity check** — verify required headings exist.
4. **Commit step**.

The file structures below are required minimum-skeletons. Add prose under each heading; one or two paragraphs per heading is enough for v0.1. The plugin grows via curated growth — initial files do not need to be exhaustive.

---

## Task 9: `references/normen/nen-3569-veiligheidsglas.md`

**Files:**
- Create: `~/.claude/plugins/local/houten-kozijnen-nl/references/normen/nen-3569-veiligheidsglas.md`

- [ ] **Step 1: Research**

Use `WebSearch` for "NEN 3569 veiligheidsglas eisen NL" and `WebFetch` on KOMO / NEN-sources. Note key facts:
- Wat NEN 3569 dekt (menselijk letsel door glasbreuk)
- Klassen 1B1 / 2B2 / 3B3 (impact-classificatie volgens EN 12600)
- Wanneer verplicht: vloerhoogte (< 850 mm), specifieke locaties (tuindeuren, schuifpuien, badkamerwanden, balustrades)

- [ ] **Step 2: Write file**

Create `references/normen/nen-3569-veiligheidsglas.md` with this structure:

```markdown
# NEN 3569 — Veiligheidsglas

## Scope van de norm

[Wat NEN 3569 regelt: voorkomen van menselijk letsel door glasbreuk in en om gebouwen.]

## Glasklassen (EN 12600)

[1B1, 2B2, 3B3 — wanneer welke klasse vereist is.]

## Wanneer is veiligheidsglas verplicht

[Concrete situaties: vloerhoogte onder 850 mm, tuindeuren, schuifpuien, badkamerwanden, balustrades, doorvalrisico.]

## Veelvoorkomende toepassingen in kozijnen

[Schuifpui-onderpaneel, tuindeur-vulling, glaspaneel naast deur, badkamerraam.]

## Praktische beslisregels voor een configurator

[Als/dan-regels die direct in validatie-logica passen.]

## Bronnen

- NEN 3569 (NEN-norm)
- Aanvullende bronnen: ...
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Glasklassen" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/normen/nen-3569-veiligheidsglas.md" && echo "OK"
```

Expected: `OK`.

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/normen/nen-3569-veiligheidsglas.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add NEN 3569 veiligheidsglas reference"
```

---

## Task 10: `references/normen/nen-2608-glasdiktebepaling.md`

**Files:**
- Create: `references/normen/nen-2608-glasdiktebepaling.md`

- [ ] **Step 1: Research**

`WebSearch` voor "NEN 2608 glasdikte berekening" en raadpleeg KOMO/NEN-bronnen.

- [ ] **Step 2: Write file**

Required structure:

```markdown
# NEN 2608 — Glasdiktebepaling

## Scope van de norm

[Vlakglas in bouwkundige toepassingen: rekenmethode voor sterkte/dikte.]

## Wanneer berekening verplicht

[Boven welke afmetingen / omstandigheden NEN 2608-berekening vereist is.]

## Belangrijkste factoren

[Glasoppervlak, ruithoogte, windbelasting, gebouwhoogte, locatie.]

## Methode (samengevat)

[Op hoofdlijnen hoe de berekening werkt — geen volledige formule, wel principe.]

## Praktische implicaties voor een configurator

[Wanneer roept de configurator een aparte berekening aan vs gangbare standaarddiktes.]

## Bronnen

- NEN 2608
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Methode" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/normen/nen-2608-glasdiktebepaling.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/normen/nen-2608-glasdiktebepaling.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add NEN 2608 glasdikte reference"
```

---

## Task 11: `references/normen/bouwbesluit-relevant.md`

**Files:**
- Create: `references/normen/bouwbesluit-relevant.md`

- [ ] **Step 1: Research**

Bouwbesluit Online (`https://rijksoverheid.bouwbesluit.com`). Focus op kozijn-relevante artikelen: ventilatie, daglicht, vluchtroutes, inbraakwerendheid, energiezuinigheid (U-waarde-eisen).

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Bouwbesluit — kozijn-relevante artikelen

> Alleen artikelen relevant voor kozijnen. Geen volledige Bouwbesluit-uitleg.

## Ventilatie

[Welke ventilatie-eisen raken kozijnen — m³/h per ruimte, ventilatieroosters in kozijnen.]

## Daglicht

[Minimum daglichtoppervlak per ruimte; impact op glasoppervlak in kozijnen.]

## Vluchtroutes

[Eisen aan deuren in vluchtroutes: draairichting, vrije doorgang, branduitwerking.]

## Inbraakwerendheid

[Wanneer inbraakwerend hang-en-sluitwerk verplicht is (bereikbare gevelelementen nieuwbouw).]

## Energie / U-waarde-eisen

[Actuele Uw-eisen voor nieuwbouw en renovatie; relatie met BENG.]

## Versies en wijzigingen

[Bouwbesluit 2012 vs Besluit bouwwerken leefomgeving (Bbl) onder Omgevingswet — let op welke geldt op het moment van toepassing.]

## Bronnen

- Bouwbesluit Online
- Bbl (Besluit bouwwerken leefomgeving)
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Ventilatie" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/normen/bouwbesluit-relevant.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/normen/bouwbesluit-relevant.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add Bouwbesluit kozijn-relevant reference"
```

---

## Task 12: `references/normen/komo-brl-0703.md`

**Files:**
- Create: `references/normen/komo-brl-0703.md`

- [ ] **Step 1: Research**

`WebSearch` voor "BRL 0703 KOMO houten gevelelementen". Hoofdbron: SKH-publicaties en KOMO.org.

- [ ] **Step 2: Write file**

Required structure:

```markdown
# KOMO / BRL 0703 — Houten gevelelementen

## Wat is BRL 0703

[Beoordelingsrichtlijn voor KOMO-attest met productcertificaat voor houten gevelelementen.]

## Eisen aan houtkwaliteit

[Houtsoort + duurzaamheidsklasse, vingerlas-eisen, knoest-/groeicriteria, vochtgehalte bij verwerking.]

## Eisen aan verbindingen

[Hoekverbindingen, afmetingen, mechanische sterkte.]

## Eisen aan afwerking

[Verflagensysteem, laagdiktes, voorbehandeling.]

## Toleranties

[Maatvoering-toleranties die BRL 0703 voorschrijft.]

## Belang voor configurator

[Welke configurator-keuzes raken BRL-conformiteit.]

## Bronnen

- BRL 0703 (SKH / KOMO)
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "houtkwaliteit" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/normen/komo-brl-0703.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/normen/komo-brl-0703.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add KOMO/BRL 0703 reference"
```

---

## Task 13: `references/domein/houtsoorten-en-kwaliteit.md`

**Files:**
- Create: `references/domein/houtsoorten-en-kwaliteit.md`

- [ ] **Step 1: Research**

`WebSearch` voor "houtsoorten kozijnen NL meranti eiken accoya grenen duurzaamheidsklasse" en raadpleeg SKH-publicaties.

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Houtsoorten voor kozijnen

## Duurzaamheidsklassen (EN 350)

[Klassen 1–5; wat ze betekenen voor toepassing zonder verduurzaming.]

## Meranti

[Eigenschappen, gangbare toepassing, prijs-segment, kwaliteitskenmerken (Dark Red Meranti).]

## Eiken

[Eigenschappen, krimp/zwel, taninen-uitloging, behandeling.]

## Accoya

[Acetylering, duurzaamheidsklasse 1, dimensiestabiliteit, kostenniveau.]

## Grenen (vingerlas)

[Vingerlas-eisen, verduurzamingsbehoefte, gangbaar gebruik.]

## Krimp- en zwelgedrag per soort

[Tabelletje: radiaal/tangentiaal krimp-percentage globaal.]

## Beslisrichtlijnen per situatie

[Welke houtsoort past bij welk type kozijn / klimaat / budget.]

## Bronnen

- SKH-publicaties
- EN 350
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Accoya" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/houtsoorten-en-kwaliteit.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/houtsoorten-en-kwaliteit.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add houtsoorten reference"
```

---

## Task 14: `references/domein/profielen-en-doorsnedes.md`

**Files:**
- Create: `references/domein/profielen-en-doorsnedes.md`

- [ ] **Step 1: Research**

`WebSearch` voor "SKH publicatie 99-04 kozijnprofielen" — toon-aangevende NL-publicatie voor profielen in houten gevelelementen.

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Profielen, dorpels en sponningen

## SKH-publicatie 99-04 als basis

[Wat de publicatie regelt: gestandaardiseerde profielen voor houten gevelelementen.]

## Gangbare kozijnprofielen

[Standaard-doorsnedes (bijv. 67×114, 67×140, 90×114, 90×140 etc.) — geef gangbare maten.]

## Stijlen en regels

[Wat stijlen en regels zijn binnen een kozijn; gangbare doorsnedes.]

## Dorpels

[Onderdorpel, bovendorpel, tussendorpel; functies en gangbare maten; afwatering.]

## Sponningen

[Glas-sponning vs. naadloze beglazing; sponningmaten en hun relatie tot glasdikte; aanslag-sponning voor draaiende delen.]

## Profielkeuze in een configurator

[Welke profiel-attributen moet een configurator vastleggen?]

## Bronnen

- SKH-publicatie 99-04
- BRL 0703
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Sponningen" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/profielen-en-doorsnedes.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/profielen-en-doorsnedes.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add profielen-en-doorsnedes reference"
```

---

## Task 15: `references/domein/glas-en-beglazing.md`

**Files:**
- Create: `references/domein/glas-en-beglazing.md`

- [ ] **Step 1: Research**

`WebSearch` voor "HR++ HR+++ triple beglazing glaslat droge natte beglazing".

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Glas en beglazing

## Beglazingstypen

[Enkelglas (zelden, alleen monumenten), dubbelglas, HR, HR+, HR++, HR+++ (triple). Korte uitleg, indicatieve Ug-waardes.]

## Opbouw isolerende beglazing

[2- vs 3-laags, gas-vulling (argon/krypton), warm-edge spacer, coatings.]

## Glaslat-systemen

[Vaste glaslat vs gemonteerde; kit/rubber afdichting.]

## Droge vs natte beglazing

[Verschil, voor- en nadelen, gangbare keuze in NL.]

## Inbouwdiepte en glasdikte

[Relatie tussen sponningmaat en glaspakket-dikte; speling rondom; afstandhouder onder.]

## Configurator-attributen

[Welke glas-keuzes legt een configurator vast?]

## Bronnen

- NPR 3577
- Productspecificaties leveranciers
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "HR++" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/glas-en-beglazing.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/glas-en-beglazing.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add glas-en-beglazing reference"
```

---

## Task 16: `references/domein/maatvoering-en-toleranties.md`

**Files:**
- Create: `references/domein/maatvoering-en-toleranties.md`

- [ ] **Step 1: Research**

`WebSearch` voor "daglichtmaat werkmaat sponningmaat houten kozijn toleranties".

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Maatvoering en toleranties

## De drie hoofdmaten

### Daglichtmaat

[De zichtbare glasopening — wat de eindgebruiker ziet.]

### Werkmaat

[De daadwerkelijke kozijn-buitenmaat (= daglichtmaat + 2× kozijnprofielbreedte).]

### Sponningmaat

[De maat van de glassponning waar het glaspakket in valt.]

## Onderlinge relatie

[Formule of vuistregel: daglichtmaat ↔ werkmaat ↔ sponningmaat.]

## Gangbare toleranties

[Maattoleranties bij productie en plaatsing; BRL 0703-richtlijnen.]

## Hoe je dit in een configurator scheidt

[Welke maat is invoer, welke afgeleid; do's en don'ts.]

## Veelvoorkomende fouten

[Verwisselen van maattype, vergeten van sponning-aftrek, vergeten van speling.]

## Bronnen

- BRL 0703
- SKH-publicaties
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Werkmaat" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/maatvoering-en-toleranties.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/maatvoering-en-toleranties.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add maatvoering-en-toleranties reference"
```

---

## Task 17: `references/domein/marges-aansluitingen-detaillering.md`

**Files:**
- Create: `references/domein/marges-aansluitingen-detaillering.md`

- [ ] **Step 1: Research**

`WebSearch` voor "voegmaat kozijn metselwerk stelmarge krimpmarge houtsoort".

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Marges, aansluitingen en detaillering

## Voegmaat kozijn ↔ metselwerk

[Gangbare voegmaten, afhankelijk van vlakheid metselwerk en isolatie-aansluiting.]

## Stelmarge

[Marge die je nodig hebt om het kozijn waterpas en lood te kunnen stellen — gangbaar 5–15 mm rondom.]

## Krimp- en zwelmarge per houtsoort

[Differentiatie per houtsoort; verschil tussen radiaal en tangentiaal.]

## Aansluitdetails

[Aansluiting onderdorpel ↔ waterkering, aansluiting bovendorpel ↔ latei, aansluiting stijlen ↔ metselwerk/spouw.]

## Kit- en band-aansluitingen

[Compriband, kit, EPDM-band — wanneer welke.]

## Configurator-keuzes

[Welke marges en aansluitkeuzes legt de configurator vast?]

## Bronnen

- SBR-publicaties
- BRL 0703
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Stelmarge" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/marges-aansluitingen-detaillering.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/marges-aansluitingen-detaillering.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add marges-aansluitingen reference"
```

---

## Task 18: `references/domein/beslag-en-hang-en-sluitwerk.md`

**Files:**
- Create: `references/domein/beslag-en-hang-en-sluitwerk.md`

- [ ] **Step 1: Research**

`WebSearch` voor "SKG sterren hang en sluitwerk inbraakwerend NEN 5096".

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Beslag en hang-en-sluitwerk

## SKG-sterren en wat ze betekenen

[★, ★★, ★★★ — inbraakwerendheidsniveaus; relatie met NEN 5096 / EN 1627.]

## Wanneer welk niveau

[Bouwbesluit-eis voor bereikbare gevelelementen; PKVW-eisen.]

## Draaiende delen

[Draairichting (DIN-links/rechts), draai-kiep, valraam, klepraam, schuifraam.]

## Scharnieren

[Veiligheidsscharnieren, gangbare types.]

## Sluitkommen en meerpuntssluitingen

[Wat in een meerpuntssluiting zit; relatie met SKG-niveau.]

## Configurator-attributen

[Welke beslag-keuzes legt een configurator vast?]

## Bronnen

- NEN 5096
- SKG-IKOB
- PKVW
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "SKG" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/beslag-en-hang-en-sluitwerk.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/beslag-en-hang-en-sluitwerk.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add beslag-en-hang-en-sluitwerk reference"
```

---

## Task 19: `references/domein/thermiek-u-waarde.md`

**Files:**
- Create: `references/domein/thermiek-u-waarde.md`

- [ ] **Step 1: Research**

`WebSearch` voor "Uw kozijn berekening Ug Uf NEN-EN-ISO 10077 BENG".

- [ ] **Step 2: Write file**

Required structure:

```markdown
# Thermiek en U-waarde

## Wat zijn Uw, Ug en Uf

[Uw = window total, Ug = glas, Uf = frame. Eenheid W/m²K.]

## Samenstelling Uw

[Vereenvoudigde formule (oppervlakte-gewogen) of verwijzing naar NEN-EN-ISO 10077.]

## Bouwbesluit-eis (actueel)

[Maximaal toegestane Uw voor nieuwbouw en renovatie — let op: actualiseer bij wijziging Bouwbesluit/Bbl.]

## BENG-context

[BENG-eisen voor nieuwbouw; rol van kozijnen daarin.]

## Indicatieve Uw per beglazingstype op houten kozijn

[Tabel: enkelglas / dubbel / HR / HR+ / HR++ / HR+++ — indicatieve Uw met hout-frame.]

## Koudebrug-aandachtspunten

[Aansluitdetail kozijn ↔ spouw; warm-edge spacers; thermische onderbreking.]

## Configurator-implicaties

[Wanneer moet de configurator een Uw-berekening triggeren of een waarde tonen?]

## Bronnen

- NEN-EN-ISO 10077-1
- Bouwbesluit / Bbl
- BENG-rekenmethodiek
```

- [ ] **Step 3: Sanity check**

```bash
grep -q "Uw" "$HOME/.claude/plugins/local/houten-kozijnen-nl/references/domein/thermiek-u-waarde.md" && echo "OK"
```

- [ ] **Step 4: Commit**

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add references/domein/thermiek-u-waarde.md
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "docs: add thermiek-u-waarde reference"
```

---

## Task 20: End-to-end verificatie

**Files:** none (verification only)

- [ ] **Step 1: Sanity-check plugin structure**

```bash
PLUGIN_DIR="$HOME/.claude/plugins/local/houten-kozijnen-nl"
ls "$PLUGIN_DIR/agents" "$PLUGIN_DIR/commands" "$PLUGIN_DIR/references/normen" "$PLUGIN_DIR/references/domein"
test -f "$PLUGIN_DIR/.claude-plugin/plugin.json" && \
test -f "$PLUGIN_DIR/README.md" && \
test -f "$PLUGIN_DIR/references/INDEX.md" && \
echo "structure OK"
```

Expected: lists all expected files; ends with `structure OK`.

- [ ] **Step 2: Validate manifest**

```bash
python3 -c "import json; json.load(open('$HOME/.claude/plugins/local/houten-kozijnen-nl/.claude-plugin/plugin.json'))" && echo "manifest OK"
```

Expected: `manifest OK`.

- [ ] **Step 3: Verify all agent and command files have frontmatter**

```bash
for f in "$HOME/.claude/plugins/local/houten-kozijnen-nl/agents/"*.md "$HOME/.claude/plugins/local/houten-kozijnen-nl/commands/"*.md; do
  head -1 "$f" | grep -q '^---$' && echo "OK: $f" || echo "MISSING FRONTMATTER: $f"
done
```

Expected: every line starts with `OK:`.

- [ ] **Step 4: Verify INDEX.md references match actual files**

```bash
PLUGIN_DIR="$HOME/.claude/plugins/local/houten-kozijnen-nl"
cd "$PLUGIN_DIR/references"
# Extract referenced paths from INDEX.md (anything in backticks ending in .md)
referenced=$(grep -oE '`[a-z/-]+\.md`' INDEX.md | tr -d '`' | sort -u)
# List actual files (excluding INDEX itself)
actual=$(find normen domein -name '*.md' | sort -u)
diff <(echo "$referenced") <(echo "$actual") && echo "INDEX in sync"
```

Expected: `INDEX in sync`.

- [ ] **Step 5: Smoke test 1 — known-answer question**

In a new Claude Code session in any directory:

```
/kozijn-vraag wanneer is veiligheidsglas verplicht?
```

Expected behaviour:
- Claude invokes `kozijn-domain-expert` via the Task tool
- The subagent reads `references/INDEX.md`
- The subagent reads `references/normen/nen-3569-veiligheidsglas.md`
- Answer in NL with bronvermelding referencing NEN 3569 and the reference file

- [ ] **Step 6: Smoke test 2 — knowledge-gap question (curated growth)**

In a Claude Code session:

```
/kozijn-vraag welke specifieke houtsoorten zijn vrijgesteld van verduurzaming volgens recente SKH-publicaties?
```

Expected behaviour:
- Subagent finds the topic isn't fully in references
- Uses `WebSearch`/`WebFetch`
- Summarises findings
- **Asks** "zal ik dit toevoegen aan `references/domein/houtsoorten-en-kwaliteit.md`?"
- Does **not** write without OK

- [ ] **Step 7: Smoke test 3 — code review with intentional defect**

Create a temporary test file `/tmp/glasRules.test.ts`:

```typescript
// Test: bewust een norm-overtreding inbouwen
function isGlassValid(heightFromFloor: number, isSafetyGlass: boolean): boolean {
  // Bug: enkelglas toegestaan op alle hoogtes
  return heightFromFloor > 0;
}
```

Then run:

```
/kozijn-review /tmp/glasRules.test.ts
```

Expected behaviour:
- `kozijn-code-reviewer` is invoked
- Reads relevant files in `references/normen/`
- Rapporteert minstens één bevinding met severity `kritiek` over ontbrekende NEN 3569-check (vloerhoogte < 850 mm)
- Output volgt het vaste rapport-format

- [ ] **Step 8: Smoke test 4 — auto-trigger**

Open a fresh Claude Code chat (no slash command) and ask:

```
Wat is de gangbare voegmaat tussen een houten kozijn en metselwerk?
```

Expected behaviour:
- Claude recognizes this as a kozijn-domain question and dispatches `kozijn-domain-expert` via the Task tool **without** explicit `/kozijn-vraag`. (If this fails, the agent's `description`-frontmatter needs sharper triggering keywords — adjust and re-test.)

- [ ] **Step 9: Final commit (if any cleanup needed)**

If smoke tests revealed issues that required tweaks (e.g., to a `description`-frontmatter), commit those:

```bash
cd "$HOME/.claude/plugins/local/houten-kozijnen-nl"
git add -A
git -c user.email=remco@rem.codes -c user.name=remcodes commit -q -m "fix: tweaks from smoke tests" || echo "nothing to commit"
```

---

## Self-review notes

**Spec coverage check:**
- Plugin location & manifest → Task 1 ✓
- README → Task 3 ✓
- `kozijn-domain-expert` agent (incl. tools, curated growth, scope) → Task 4 ✓
- `kozijn-code-reviewer` agent (incl. report format, severity) → Task 5 ✓
- Both slash commands → Tasks 6, 7 ✓
- All 4 norm reference files → Tasks 9–12 ✓
- All 7 domein reference files → Tasks 13–19 ✓
- INDEX.md → Task 8 + verified in sync in Task 20 ✓
- All 4 smoke tests from spec → Task 20 steps 5–8 ✓
- Sanity checks (manifest valid, frontmatter, INDEX sync) → Task 20 steps 1–4 ✓

**Placeholder scan:** Reference-content tasks (9–19) deliberately specify required headings rather than ghostwritten prose. This is not a placeholder — the prose under each heading is substantive content the implementer must produce via WebSearch/WebFetch against authoritative sources, with the explicit instruction to mark unverified claims with "let op: te verifiëren". Norm-numbers and headings are concrete; the prose is the implementer's deliverable, much like a programmer fills in function bodies given a signature.

**Type/name consistency:** Agent names (`kozijn-domain-expert`, `kozijn-code-reviewer`) and command names (`kozijn-vraag`, `kozijn-review`) are used identically across all tasks. File paths in the File Structure table match all task headers.
