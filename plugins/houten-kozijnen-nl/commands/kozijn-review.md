---
description: Review kozijn-configurator-code tegen NL-normen via de kozijn-code-reviewer subagent
argument-hint: <pad/naar/bestand-of-dir of "huidige diff">
---

Roep **verplicht** de subagent `kozijn-code-reviewer` aan via de Task-tool (`subagent_type: kozijn-code-reviewer`) met de onderstaande scope. Voer de review NIET zelf uit.

De plugin-references (de meetlat) staan onder `${CLAUDE_PLUGIN_ROOT}/references/`. De subagent moet eerst `${CLAUDE_PLUGIN_ROOT}/references/INDEX.md` lezen, dan de relevante normen/domein-files, en daarna pas de gebruikerscode beoordelen.

Te reviewen scope:

$ARGUMENTS

Als de scope "huidige diff" is, instrueer de subagent om `git diff` te gebruiken voor de te reviewen wijzigingen. Geef het volledige rapport letterlijk terug in het vaste format.
