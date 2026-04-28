# Thermiek en U-waarde

## Wat zijn Uw, Ug en Uf

Bij ramen en kozijnen worden drie afzonderlijke warmtedoorgangscoëfficiënten gebruikt. Alle drie hebben de eenheid W/m²K (watt per vierkante meter per kelvin): hoe lager de waarde, hoe beter de isolatie.

- **Ug** (glass): de thermische prestatie van het glaspakket zelf, zonder rekening te houden met het kozijn.
- **Uf** (frame): de warmtedoorgang via het kozijnprofiel. Voor houten kozijnen ligt Uf doorgaans tussen 0,85 en 2,1 W/m²K, afhankelijk van houtsoort, profieldiepte en eventuele thermische verbeteringen (bijv. Accoya of geïsoleerde kern).
- **Uw** (window): de totale warmtedoorgang van het complete raamelementcombinatie — glas, kozijn én de lineaire randaansluiting van de beglazing. Dit is de waarde die wordt getoetst aan het Bouwbesluit/Bbl.

## Samenstelling Uw

De Uw-waarde wordt berekend volgens NEN-EN-ISO 10077-1 met de volgende oppervlakte- en omtrekgewogen formule:

```
Uw = (Ug · Ag + Uf · Af + Ψg · Lg) / (Ag + Af)
```

Waarbij:

| Symbool | Betekenis |
|---------|-----------|
| Ag | Oppervlakte beglazing [m²] |
| Af | Oppervlakte kozijnprofielen [m²] |
| Ug | U-waarde glas [W/m²K] |
| Uf | U-waarde kozijnprofiel [W/m²K] |
| Ψg | Lineaire warmtedoorgang rand beglazing (spacer) [W/mK] |
| Lg | Omtreklengte van het glaspakket [m] |

De term `Ψg · Lg` vertegenwoordigt de invloed van de afstandhouder (spacer) aan de glasrand. Bij een standaard aluminium spacer geldt Ψg ≈ 0,08 W/mK; bij een warm-edge spacer daalt dit naar ≈ 0,04–0,06 W/mK. Hierdoor kan de Uw met 0,1–0,3 W/m²K verbeteren.

Voor een snelle schatting — of wanneer Lg niet bekend is — wordt in de praktijk ook een vereenvoudigde oppervlaktegewogen berekening gebruikt:

```
Uw ≈ (Ug · Ag + Uf · Af) / (Ag + Af)
```

Deze benadering verwaarloost de randterm en geeft een enigszins te optimistische uitkomst; voor een formele aanvraag of BENG-berekening is de volledige NEN-EN-ISO 10077-formule vereist.

## Bouwbesluit-eis (actueel)

> **let op:** de onderstaande waarden zijn van kracht per medio 2025 (Bbl, geconsolideerde versie). Actualiseer bij elke wijziging van het Besluit bouwwerken leefomgeving (Bbl).

**Nieuwbouw (woonfuncties en gebruiksfuncties):**

| Toepassing | Maximale Uw |
|------------|------------|
| Elk afzonderlijk raam/kozijn/deur | 2,2 W/m²K |
| Gemiddeld over alle ramen, kozijnen en deuren | 1,65 W/m²K |
| Constructieonderdelen gelijkgesteld aan ramen (bijv. niet-ventilerende borstweringen) | 1,65 W/m²K |

**Renovatie/verbouw** (vervanging van kozijnen in de thermische schil):

| Toepassing | Maximale Uw | Grondslag |
|------------|------------|-----------|
| Vervanging raam, kozijn of deur | 2,2 W/m²K | Art. 5.20, lid 2 Bbl |

Uitzondering: als het rechtmatig verkregen prestatieniveau beter is dan 2,2 W/m²K, geldt dat betere niveau als eis.

Bij ingrijpende renovatie (meer dan 25 % van de gebouwschil vernieuwd of uitgebreid) gelden de nieuwbouweisen.

## BENG-context

BENG (Bijna Energieneutrale Gebouwen) is de Nederlandse invulling van de Europese bijna-energieneutrale gebouwenstandaard en is per 1 januari 2021 verplicht voor alle nieuwbouw. De eisen worden berekend via de NTA 8800-rekenmethodiek en omvatten drie indicatoren:

1. **BENG 1** – Maximale energiebehoefte [kWh/m²jaar] — hier speelt de Uw direct mee: een lager Uw verlaagt de transmissieverliezen en dus de energiebehoefte.
2. **BENG 2** – Maximaal primair fossiel energiegebruik [kWh/m²jaar].
3. **BENG 3** – Minimaal aandeel hernieuwbare energie [%].

De minimumeis in het Bbl (Uw ≤ 1,65 W/m²K gemiddeld) is de ondergrens. Voor BENG 1 is in de NTA 8800-berekening de werkelijke Uw per raamopeningtype nodig; betere kozijnen (lagere Uw) verlagen de berekende energiebehoefte rechtstreeks. Bij nul-op-de-meter of passiefbouwprojecten wordt doorgaans gestreefd naar Uw ≤ 0,8 W/m²K.

Houten kozijnen hebben van nature een gunstige Uf-waarde en sluiten goed aan op de randdetaillering die voor BENG-berekeningen relevant is. De keuze voor het glaspakket is echter bepalend voor het eindresultaat.

## Indicatieve Uw per beglazingstype op houten kozijn

> **let op:** onderstaande waarden zijn indicatief en afhankelijk van kozijngeometrie (verhouding glasoppervlak / kozijnoppervlak), profieldiepte, houtsoort en type spacer. Gebruik NEN-EN-ISO 10077 of leveranciersberekeningen voor officiële waarden.

Aannames: houten kozijn met Uf ≈ 1,8 W/m²K (standaard naaldhout, gedroogd), glasaandeel ≈ 70 %, standaard spacer tenzij anders vermeld.

| Beglazingstype | Ug (W/m²K) | Indicatieve Uw (W/m²K) | Voldoet Bbl nieuwbouw (≤ 1,65 gem.) |
|----------------|------------|------------------------|--------------------------------------|
| Enkelglas | ≈ 5,9 | ≈ 4,7 | Nee |
| Dubbelglas (oud) | ≈ 2,8 | ≈ 2,5 | Nee |
| HR-glas | ≈ 1,9 | ≈ 1,9 | Marginaal / nee |
| HR+-glas | ≈ 1,5 | ≈ 1,6 | Grens (afhankelijk van kozijn) |
| HR++-glas | ≈ 1,1 | ≈ 1,3 | Ja |
| HR+++-glas (triple) | ≈ 0,6 | ≈ 0,9 | Ja, ook voor passief/NOM |

Met een beter geïsoleerd houten kozijn (Uf ≈ 1,2 W/m²K, bijv. Accoya of geïsoleerde kern) en warm-edge spacer zijn Uw-waarden van ≈ 0,80 W/m²K haalbaar met HR+++-beglazing.

## Koudebrug-aandachtspunten

**Randaansluiting kozijn ↔ spouwmuur:**
De aansluiting van het kozijn op de spouw is een klassiek koudebrugpunt. Het kozijn moet thermisch worden ingebed in het isolatiepakket (warm opstellen). Doorgaans wordt het kozijn zo geplaatst dat de binnenkant van het glasvlak aansluit op de binnenzijde van de spouwisolatie. Een onjuiste positie verhoogt de kans op condensatie aan de binnenzijde en verhoogt de lineaire warmtedoorgang Ψ aan de kozijn-spouwrand.

**Warm-edge spacers:**
De afstandhouder (spacer) in het glaspakket vormt een thermische brug aan de glasrand. Klassieke aluminium spacers hebben een hoge geleidbaarheid (λ ≈ 160 W/mK). Warm-edge spacers van kunststof composiet, roestvast staal of thermobare materialen reduceren Ψg van ≈ 0,08 naar ≈ 0,04–0,06 W/mK. Dit verbetert de Uw, vermindert condensatierisico aan de glasrand en verhoogt het glasrandcomfort.

**Houten kozijnen en thermische onderbreking:**
Bij aluminium kozijnen is een aparte thermische onderbreking noodzakelijk. Hout heeft een lage warmtegeleidbaarheid (λ ≈ 0,13 W/mK voor naaldhout) en vormt daarmee zelf al een effectieve thermische scheiding. Een aparte thermische onderbreking is dus normaliter niet nodig, mits het profiel voldoende diep is. Bij slanke, smalle profielen kan een geïsoleerde kern of Accoya-hout de Uf verder verlagen.

**Condensatie-check:**
Bij renovatieprojecten waarbij de isolatiewaarde van het kozijn sterk verbeterd wordt (bijv. van enkel naar HR++) moet de dauwpuntberekening van de aangrenzende muurconstructie worden gecontroleerd. De verhoogde binnenwandtemperatuur bij het kozijn kan het dauwpunt verplaatsen naar elders in de constructie.

## Configurator-implicaties

De volgende situaties vereisen dat de configurator een Uw-indicatie toont of een berekening triggert:

1. **Beglazingskeuze:** Bij elke selectie van een glaspakket (enkel, dubbel, HR, HR+, HR++, HR+++) moet de bijbehorende indicatieve Uw direct zichtbaar zijn, zodat de gebruiker direct ziet of aan de Bbl-eis wordt voldaan.

2. **Bbl-toetsing:** Toon een visuele markering (bijv. groen/rood) of tekstindicatie wanneer de geselecteerde configuratie de nieuwbouwdrempel van Uw ≤ 2,2 W/m²K (per element) resp. ≤ 1,65 W/m²K (gemiddeld) haalt of overschrijdt.

3. **Toepassing renovatie vs. nieuwbouw:** Bij het invoerproces van de projectcontext (nieuwbouw of renovatie) moet de juiste eis worden gehanteerd. Bij renovatie geldt 2,2 W/m²K; bij nieuwbouw 1,65 W/m²K gemiddeld.

4. **Warm-edge spacer optie:** Wanneer de gebruiker een warm-edge spacer selecteert, moet de Uw-weergave worden bijgesteld (indicatief −0,1 tot −0,2 W/m²K t.o.v. standaard spacer).

5. **Afwijkende kozijngeometrie:** Bij grote raamoppervlakken (meer dan 80 % glasaandeel) of samengestelde elementen (koppelkozijnen, seriecombinaties) is een nauwkeuriger berekening gewenst; de configurator kan doorverwijzen naar een maatwerk Uw-berekening of de klant adviseren contact op te nemen.

6. **BENG-adviescontext:** Bij nieuwbouwprojecten kan de configurator vermelden of de gekozen combinatie ook geschikt is voor passief- of NOM-bouw (streefwaarde Uw ≤ 0,8 W/m²K).

## Bronnen

- NEN-EN-ISO 10077-1: *Thermal performance of windows, doors and shutters — Calculation of thermal transmittance — Part 1: General* (NEN, Delft)
- Bouwbesluit / Besluit bouwwerken leefomgeving (Bbl) — art. 5.20 (verbouw) en de energieprestatie-eisen nieuwbouw; zie [iplo.nl](https://iplo.nl/regelgeving/regels-voor-activiteiten/technische-bouwactiviteit/)
- BENG-rekenmethodiek: NTA 8800 *Energieprestatie van gebouwen* (NEN, Delft); zie ook [RVO.nl BENG](https://www.rvo.nl/onderwerpen/wetten-en-regels-gebouwen/energieprestatie-eisen-verbouw-renovatie)
- Kenniscentrum Glas / kennisbron beglazing: [glasinbeeld.nl](https://www.glasinbeeld.nl) — Ug-waarden en spacer-prestaties
