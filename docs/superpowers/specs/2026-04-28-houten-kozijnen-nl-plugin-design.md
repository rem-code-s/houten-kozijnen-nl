# Design: `houten-kozijnen-nl` Claude Code plugin

**Datum:** 2026-04-28
**Status:** Goedgekeurd ontwerp, klaar voor implementatieplan
**Auteur:** Brainstorm-sessie tussen gebruiker en Claude

## Doel

Een Claude Code plugin die ondersteunt bij het bouwen van een kozijn-configurator voor houten kozijnen op de Nederlandse markt. De plugin levert:

1. **Domeinkennis** over houten kozijnen, glas, profielen, maatvoering, marges en relevante NL-normen.
2. **Code-review** die de configurator-codebase (TypeScript/JavaScript) toetst aan die normen en gaten signaleert.

De normen vormen de waarheid; de codebase wordt eraan gemeten. De gebruiker heeft expliciet aangegeven niet zeker te zijn of de huidige codebase volledig norm-conform is — de plugin moet juist die afwijkingen kunnen vinden.

## Scope

**In scope:**
- Houten kozijnen (geen kunststof, geen aluminium)
- Nederlandse markt: NEN-normen, KOMO/BRL 0703, Bouwbesluit
- Twee subagents: domain-expert + code-reviewer
- Twee slash commands als shortcuts naar die agents
- Reference-files met onafhankelijke normkennis
- Curated knowledge growth: agent stelt voor wat aan references toe te voegen, gebruiker keurt goed
- Web-fallback (`WebFetch`/`WebSearch`) voor de domain-expert bij kennisgaten

**Out of scope (YAGNI):**
- Marketplace-publicatie (kan later)
- MCP-server met deterministische rekenmodules (te vroeg)
- Andere materialen dan hout
- EU/EN-normen buiten wat via NL-context relevant is
- Automatische code-fixes door de reviewer
- Geautomatiseerde tests (smoke tests handmatig zijn voldoende)

## Architectuur

### Locatie & vorm

- Lokale plugin in `~/.claude/plugins/local/houten-kozijnen-nl/`
- Geen git remote vereist; manifest aanwezig zodat later promotie naar marketplace mogelijk is
- Versie 0.1.0

### Bestandsstructuur

```
houten-kozijnen-nl/
├── .claude-plugin/
│   └── plugin.json
├── README.md
├── agents/
│   ├── kozijn-domain-expert.md
│   └── kozijn-code-reviewer.md
├── commands/
│   ├── kozijn-vraag.md
│   └── kozijn-review.md
└── references/
    ├── INDEX.md
    ├── normen/
    │   ├── nen-3569-veiligheidsglas.md
    │   ├── nen-2608-glasdiktebepaling.md
    │   ├── bouwbesluit-relevant.md
    │   └── komo-brl-0703.md
    └── domein/
        ├── houtsoorten-en-kwaliteit.md
        ├── profielen-en-doorsnedes.md
        ├── glas-en-beglazing.md
        ├── maatvoering-en-toleranties.md
        ├── marges-aansluitingen-detaillering.md
        ├── beslag-en-hang-en-sluitwerk.md
        └── thermiek-u-waarde.md
```

### Splitsing `normen/` vs `domein/`

- `normen/` bevat de meetlat: wettelijke en certificeringseisen. De code-reviewer leunt hier zwaar op.
- `domein/` bevat praktijkkennis: hoe wordt het in de werkelijkheid gedaan, gangbare keuzes, terminologie. De domain-expert gebruikt beide; de reviewer raadpleegt domein bij bevindingen die context nodig hebben.

### `references/INDEX.md`

Eén regel per referentie-bestand met pad, beschrijving en topic-keywords. Agents lezen dit eerst en laden vervolgens gericht alleen de bestanden die relevant zijn voor de huidige vraag — token-efficiency.

## Componenten

### Agent: `kozijn-domain-expert`

**Doel:** beantwoordt domeinvragen over houten kozijnen, glas, profielen, normen en praktijk. Geen code-review.

**Frontmatter:**
- `name: kozijn-domain-expert`
- `description`: zo geformuleerd dat Claude deze agent automatisch via de Task-tool inschakelt bij vragen over kozijnen, glas, profielen, normen, maatvoering en aanverwante topics
- `tools`: `Read`, `Glob`, `Grep`, `WebFetch`, `WebSearch`, `Edit`, `Write`

**Tool-toegang:**
- Edit/Write expliciet toegestaan, maar alléén voor bestanden onder `references/` (curated growth). De systeem-prompt instrueert de agent dit te respecteren.

**Werkwijze (uit te werken in systeem-prompt):**
1. Rol: NL-expert houten kozijnen, KOMO-gecertificeerd denkraam, hout-only.
2. Bij vragen: lees eerst `references/INDEX.md`, daarna gericht relevante files.
3. Bij kennisgat: gebruik WebFetch/WebSearch, vat resultaat samen, vraag actief of de geleerde info naar `references/...` toegevoegd mag worden. Schrijf alleen na expliciete OK van de gebruiker.
4. Output: NL, concreet, met bronvermelding (welke norm/welk artikel), geen vage praat.
5. Buiten scope (kunststof, aluminium, niet-NL markten): expliciet aangeven dat dit buiten de expertise valt.

### Agent: `kozijn-code-reviewer`

**Doel:** toetst configurator-code (TypeScript/JavaScript) aan de normen, rapporteert afwijkingen.

**Frontmatter:**
- `name: kozijn-code-reviewer`
- `description`: triggert op review-vragen over kozijn-configurator-code, validatieregels, business-logic
- `tools`: `Read`, `Glob`, `Grep`, `WebFetch`

**Tool-toegang:**
- Geen Edit/Write — reviewer rapporteert, mens beslist en past aan.

**Werkwijze (uit te werken in systeem-prompt):**
1. Stel de scope vast (bestand, directory, of "huidige diff" via `git diff`).
2. Lees relevante `references/normen/*` en waar nodig `references/domein/*` voor de meetlat.
3. Bij verdiepende domeinvragen: roep `kozijn-domain-expert` aan via de Task-tool.
4. Output volgens vast rapport-format (zie hieronder).
5. Output altijd in NL.

**Rapport-format (vast):**
- **Bevinding** — wat klopt niet of is twijfelachtig
- **Norm/bron** — welke NEN/Bouwbesluit-regel of best-practice raakt het
- **Locatie** — bestand + regelnummer in de codebase
- **Voorstel** — concrete fix-richting (geen volledige code)
- **Severity** — `kritiek` (norm-overtreding/veiligheid), `belangrijk` (afwijking best-practice), `nit` (cosmetisch)

**Bewust niet:**
- Geen automatische code-fixes
- Geen niet-kozijn-feedback (geen "rename deze variabele"-soort review)

### Slash command: `/kozijn-vraag <vraag>`

Dunne wrapper (paar regels markdown) die `kozijn-domain-expert` via de Task-tool aanroept met de meegegeven vraag.

### Slash command: `/kozijn-review <pad of beschrijving>`

Dunne wrapper die `kozijn-code-reviewer` via de Task-tool aanroept. Argument kan zijn:
- Een bestandspad
- Een directory-pad
- De string "huidige diff" — reviewer voert dan zelf `git diff` uit binnen de scope

## Reference-files: scope per bestand

### `normen/`

- **`nen-3569-veiligheidsglas.md`** — wanneer veiligheidsglas verplicht is (vloerhoogte, locatie, type ruit), klassen 1B1/2B2, praktijksituaties (tuindeur, schuifpui, balustrade).
- **`nen-2608-glasdiktebepaling.md`** — principe glasdikteberekening, factoren (oppervlak, windbelasting, ruithoogte), wanneer berekening verplicht is.
- **`bouwbesluit-relevant.md`** — alleen kozijn-relevante artikelen: ventilatie, daglicht, vluchtroutes, inbraakwerendheid, energie (U-waarde-eisen).
- **`komo-brl-0703.md`** — BRL 0703 voor houten gevelelementen: certificering, eisen aan houtkwaliteit, verbindingen, afwerking.

### `domein/`

- **`houtsoorten-en-kwaliteit.md`** — meranti, eiken, accoya, grenen: duurzaamheidsklassen, gangbare toepassingen, krimp/zwel-gedrag.
- **`profielen-en-doorsnedes.md`** — gangbare kozijnprofielen (SKH-publicatie 99-04), doorsnedes, dorpels, stijlen, sponningmaten.
- **`glas-en-beglazing.md`** — HR++/HR+++/triple, gangbare opbouw, glaslat-systemen, inbouwdiepte, droge/natte beglazing.
- **`maatvoering-en-toleranties.md`** — daglichtmaten vs werkmaten vs sponningmaten, gangbare toleranties, hoe je deze in een configurator scheidt.
- **`marges-aansluitingen-detaillering.md`** — voegmaten metselwerk/spouw, stelmarges, krimp/zwel-marges per houtsoort.
- **`beslag-en-hang-en-sluitwerk.md`** — SKG-sterren (1/2/3), draairichtingen, gangbare beslagsystemen.
- **`thermiek-u-waarde.md`** — U-waarde-eisen Bouwbesluit, samenstelling Uw uit Ug + Uf, koudebrug-aandachtspunten.

## Knowledge growth (curated)

Mechanisme: tijdens gebruik kan de domain-expert WebFetch/WebSearch inzetten om kennisgaten te overbruggen. Na het beantwoorden van de vraag stelt de expert actief voor om de geleerde info aan een specifiek `references/...`-bestand toe te voegen. **Toevoegen gebeurt alleen na expliciete OK van de gebruiker.** De plugin groeit zo organisch maar onder regie.

`INDEX.md` moet meegroeien wanneer er bestanden toegevoegd worden — onderdeel van het schrijfvoorstel van de agent.

## Verificatie

**Smoke tests (handmatig in echte Claude Code-sessie):**

1. `/kozijn-vraag wanneer is veiligheidsglas verplicht?` → expert citeert NEN 3569 en heeft `references/normen/nen-3569-veiligheidsglas.md` gelezen.
2. `/kozijn-vraag <bewust obscure vraag>` → expert gebruikt WebFetch, vat samen, vraagt of het in `references/` mag.
3. `/kozijn-review <test-bestandje met opzettelijke norm-afwijking>` → reviewer rapporteert volgens het vaste format met severity.
4. Auto-trigger: nieuwe chat met vraag als "wat is de gangbare voegmaat tussen kozijn en metselwerk?" → Claude moet uit zichzelf de domain-expert via Task-tool inschakelen op basis van de `description`-frontmatter.

**Sanity checks op de plugin zelf:**
- `plugin.json` valid JSON met juiste velden
- Alle agent-bestanden hebben correcte frontmatter (`name`, `description`, optioneel `tools`)
- Slash-command-bestanden hebben correcte frontmatter en roepen de juiste agent aan
- `INDEX.md` is synchroon met de inhoud van `references/`

Geen geautomatiseerde tests; voor een statische kennisplugin is dat overkill.

## Open kwesties

Geen op moment van schrijven. Alle ontwerpkeuzes zijn expliciet gemaakt en goedgekeurd in de brainstorm.

## Volgende stap

Implementatieplan opstellen via de `superpowers:writing-plans` skill, op basis van dit ontwerp.
