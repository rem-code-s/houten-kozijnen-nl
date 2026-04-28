---
description: Stel een vraag over houten kozijnen aan de kozijn-domain-expert subagent
argument-hint: <jouw vraag in NL>
---

Roep **verplicht** de subagent `kozijn-domain-expert` aan via de Task-tool (`subagent_type: kozijn-domain-expert`) met de onderstaande vraag. Antwoord NIET zelf — laat de subagent het werk doen.

De plugin-references staan onder `${CLAUDE_PLUGIN_ROOT}/references/`. De subagent moet eerst `${CLAUDE_PLUGIN_ROOT}/references/INDEX.md` lezen, dan gericht de relevante reference-files, en pas WebFetch/WebSearch gebruiken als die files het onderwerp aantoonbaar niet dekken.

Vraag van de gebruiker:

$ARGUMENTS

Geef het volledige antwoord van de subagent letterlijk terug, inclusief bronvermelding.
