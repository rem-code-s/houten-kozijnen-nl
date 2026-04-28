---
description: Review kozijn-configurator-code tegen NL-normen via de kozijn-code-reviewer subagent
argument-hint: <pad/naar/bestand-of-dir of "huidige diff">
---

Roep de subagent `kozijn-code-reviewer` aan via de Task-tool met deze scope:

$ARGUMENTS

Als de scope "huidige diff" is, instrueer de subagent om `git diff` te gebruiken voor de te reviewen wijzigingen. Geef het volledige rapport terug in het vaste format.
