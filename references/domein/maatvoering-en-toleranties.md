# Maatvoering en toleranties

## De drie hoofdmaten

### Daglichtmaat

De daglichtmaat (ook wel *dagmaat* of *doorkijkmaat* genoemd) is de netto binnenwerkse afstand die de eindgebruiker ziet wanneer hij recht voor het kozijn staat: de zichtbare glasopening, gemeten tussen de binnenvlakken van de stijlen en dorpels. Het is de vrije ruimte die overblijft nádat het kozijn is geplaatst en de sponning is meegerekend. Bij het bestellen van zonwering, horren of ingebouwde jaloezieën is de daglichtmaat de sturende maat.

De daglichtmaat is kleiner dan de sponningmaat, omdat de sponningaanslag (de lip waarachter het glas valt) aan alle kanten van de opening aftrekt.

### Werkmaat

De werkmaat is de daadwerkelijke buitenwerkse afmeting van het kozijn zelf, gemeten van buitenkant linkerstijl tot buitenkant rechterstijl (en van buitenkant boven- tot buitenkant onderdorpel). Dit is de maat die de timmerfabriek produceert en die in het bestek of op de werktekening staat vermeld. In de praktijk wordt de werkmaat ook wel *buitenwerkse maat* of *kozijnmaat* genoemd.

De werkmaat is gelijk aan de daglichtmaat vermeerderd met tweemaal de kozijnprofielbreedte:

```
Werkmaat = Daglichtmaat + (2 × kozijnprofielbreedte)
```

Voorbeeld: bij een daglichtmaat van 900 mm en een profielbreedte van 67 mm is de werkmaat 900 + 2 × 67 = 1034 mm.

De werkmaat is altijd kleiner dan de dagmaat van de muuropening, omdat er rondom stelruimte (speling voor plaatsing en uitlijning) nodig is.

### Sponningmaat

De sponningmaat is de afmeting van de glassponning, gemeten van sponningbodem tot sponningbodem aan de tegenoverliggende zijde. Dit is de ruimte waarbinnen het glaspakket (of het draaiende raam) valt. De sponningmaat is groter dan de daglichtmaat en kleiner dan de werkmaat.

Minimale sponninghoogte (diepte van de sponning) voor kozijnen, ramen en schuifdeuren bedraagt volgens KVT **minimaal 17 mm**; voor buitendeuren geldt doorgaans 18 mm of meer (let op: controleer per fabrikant en glazingssysteem).

De maat van het te bestellen glaspakket wordt afgeleid van de sponningmaat door de vereiste omtrekspeling af te trekken:

```
Glasmaat = Sponningmaat − 2 × omtrekspeling (per richting)
```

Standaard wordt een omtrekspeling van **5 mm per zijde** (dus 10 mm per richting) aangehouden, zodat het glas vrij kan uitzetten en er ruimte is voor glasblokken.

---

## Onderlinge relatie

De drie maten staan in een vaste hiërarchische relatie:

```
Sponningmaat  >  Daglichtmaat  >  (Glasmaat)
Werkmaat      >  Sponningmaat  (werkmaat omvat de volledige kozijnprofielen)
```

Schematisch:

```
|<----------------------- Werkmaat ----------------------->|
         |<-------- Sponningmaat -------->|
                  |<-- Daglichtmaat -->|
                           |<- Glasmaat ->|
```

Vuistregel voor de aftrekken per kant:

| Van … naar … | Aftrek per kant (typisch) |
|---|---|
| Werkmaat → Sponningmaat | = kozijnprofielbreedte (bijv. 67 of 90 mm) |
| Sponningmaat → Daglichtmaat | = sponningaanslag (doorgaans 10–15 mm) |
| Sponningmaat → Glasmaat | = omtrekspeling (KVT: min. 5 mm per zijde) |

*Let op: exacte waarden hangen af van het gekozen profiel (67 × 90, 90 × 102, enz.) en het glazingssysteem. Raadpleeg altijd de profieltekening van de fabrikant.*

Alternatieve vuistregel voor glasmaat vanuit dagmaat (bij vervanging van bestaand glas):

```
Glasmaat ≈ Daglichtmaat + 24 mm  (= 2 × 12 mm sponningaanslag, per richting)
```

*Let op: de 24 mm is een praktijkgemiddelde dat per kozijntype kan variëren; meten vanuit de sponning heeft de voorkeur.*

---

## Gangbare toleranties

### Productietoleranties (timmerfabriek)

Houten kozijnen worden met CNC-machines geproduceerd; maatafwijkingen bij machinaal uitgefreesd hout zijn doorgaans kleiner dan **±0,5 mm**. De KVT (Kwaliteit van houten gevelelementen, het handboek behorend bij BRL 0801) legt de maximaal toelaatbare maatafwijkingen vast in hoofdstuk **63 "Maximaal toelaatbare maatafwijkingen"**.

BRL 0801 schrijft voor dat fabrikanten met een KOMO-certificaat hun productieproces beheersen conform de BRL-eisen. *Let op: de specifieke tolerantiewaarden uit BRL 0801/KVT paragraaf 63 zijn niet vrij beschikbaar; raadpleeg het SKH- of Kiwa-certificatiedocument of de licentienemer voor de exacte tabel.*

Praktische bandbreedtes die in de branche gangbaar zijn:

| Onderdeel | Gangbare productietolerantie |
|---|---|
| Kozijnhout (gezaagd/geschaafd) | ±1 mm op de nominale maat |
| Machinaal uitgefreesd profiel | ±0,5 mm |
| Geassembleerd kozijn (werkmaat) | ±2 mm (let op: fabrikantspecificatie leidend) |

### Plaatsingstoleranties (montage op de bouwplaats)

Bij het inmeten en plaatsen van het kozijn in het bouwkundig kader gelden ruimere toleranties:

- **Stelruimte rondom kozijn**: minimaal 10–15 mm per zijde (afhankelijk van het type draagconstructie en het te gebruiken bevestigings- en kimsysteem).
- **Uitlijning (loodrecht/haaks)**: maximaal 2 mm afwijking over de hoogte van het kozijn (conform gangbare bouwpraktijk).
- **Omtrekspeling raam in kozijn**: hinge-zijde 2 mm, sluitende zijde raam 2,5–3 mm, sluitende zijde deur 3–4 mm, onderkant draaikiepraam 3–5 mm (KVT/SKH-richtlijn).

*Let op: BRL 0801 betreft uitsluitend houten gevelelementen. BRL 0703 geldt voor kunststof (PVC) kozijnen. Verwar de twee niet.*

---

## Hoe je dit in een configurator scheidt

**Invoermaat**: in een configurator is de **werkmaat** de primaire invoer. Dit is de maat die de koper opgeeft en die in het bestek staat; het is wat de fabrikant maakt.

**Afgeleide maten**:
- De daglichtmaat wordt automatisch berekend als `werkmaat − (2 × kozijnprofielbreedte)`.
- De sponningmaat volgt uit `werkmaat − (2 × (kozijnprofielbreedte − sponningaanslag))`, of wordt opgehaald uit de profielbibliotheek.
- De glasmaat wordt afgeleid van de sponningmaat minus de omtrekspeling.

**Do's**:
- Toon de daglichtmaat expliciet als informatief veld zodat de koper begrijpt wat hij "ziet".
- Genereer de glasmaat als verborgen veld voor de productielijst, nooit als invoerveld.
- Sla per profieltype de sponningaanslag op als constante in de profielbibliotheek.
- Valideer dat de werkmaat deelbaar is door gangbare profielmaten (pas op voor halve millimeters).

**Don'ts**:
- Laat de gebruiker nooit de sponningmaat zelf invullen — die is altijd afgeleid.
- Vermeng de maten niet in een enkel invoerveld zonder label.
- Gebruik nooit een ruwe "− 20 mm" aftrek als hardcoded constante voor alle profielen; de aftrek hangt af van het gekozen profiel.

---

## Veelvoorkomende fouten

1. **Daglichtmaat en werkmaat verwisselen**: de koper geeft de dagopening in de muur op (dagmaat) als werkmaat. Resultaat: het kozijn is te groot voor de opening. Oplossing: vraag altijd expliciet "buitenwerkse kozijnmaat" of bereken de werkmaat uit de muuropening minus stelruimte.

2. **Sponning-aftrek vergeten bij glasbestelling**: de glasmaat wordt gelijkgesteld aan de sponningmaat. Het glas past niet of klem in de sponning. Altijd minimaal 5 mm omtrekspeling per kant aftrekken.

3. **Stelruimte niet meenemen**: de werkmaat wordt gelijkgesteld aan de muuropening. Bij plaatsing is er geen ruimte om het kozijn uit te lijnen. Reken altijd 10–15 mm stelruimte per kant in.

4. **Profieldikte als constante behandelen**: één hardcoded aftrek voor alle profielen terwijl het assortiment zowel 67 mm als 90 mm dikke profielen bevat. Gebruik de profielbibliotheek als bron.

5. **Omtrekspeling raam vergeten**: bij een draai-kiepraam wordt de glasmaat correct berekend, maar de omtrekspeling tussen het raam en het kozijn (2–5 mm per zijde) wordt niet meegenomen. Het raam sluit niet of schaaft tegen het kozijn.

6. **Hoogte en breedte omwisselen**: in Nederland is de kozijnmaat-conventie *breedte × hoogte*. Fouten ontstaan wanneer API's of spreadsheets de volgorde omdraaien.

---

## Bronnen

- **BRL 0801** (SKH/Kiwa) — *Komo Beoordelingsrichtlijn voor houten gevelelementen*; leidende NL-norm voor houten kozijnen, ramen en deuren. Beheerd door SKH (Stichting Keuringsbureau Hout) en gecertificeerd via Kiwa.
- **SKH-publicaties** — [skh.nl](https://www.skh.nl/expertise/keurmerken/komo/brl-0801-houten-gevelelementen-kozijnen/)
- **KVT** (*Kwaliteit van houten gevelelementen*) — praktijkhandboek behorend bij BRL 0801; online raadpleegbaar via [kvt-online.nl](https://kvt-online.nl/). Relevante secties: 12.2 (sponningbenamingen en -afmetingen), 30 (toelaatbare afmetingen kozijnen), 61 (profielen en houtafmetingen), 63 (maximaal toelaatbare maatafwijkingen).
- **Glasmaatje.nl** — [glas-in-kozijnen-opmeten](https://glasmaatje.nl/Glas-in-kozijnen-opmeten/) — praktische uitleg sponningmaat en glasmaat.
- **Select Windows** — [wat-betekenen-al-die-kozijnmaten](https://www.selectwindows.nl/wat-betekenen-al-die-kozijnmaten/) — eindgebruikersuitleg.
- **Schildersvak.nl** — [omtrekspeling-verdient-aandacht](https://www.schildersvak.nl/artikelen/omtrekspeling-verdient-aandacht/) — gangbare spelingsmaten.
