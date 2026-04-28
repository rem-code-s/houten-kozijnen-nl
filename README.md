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
