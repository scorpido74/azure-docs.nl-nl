---
title: De modelconversie configureren
description: Beschrijving van alle modelconversieparameters
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681569"
---
# <a name="configure-the-model-conversion"></a>De modelconversie configureren

In dit hoofdstuk worden de opties voor de modelconversie beschreven.

## <a name="settings-file"></a>Bestand Instellingen

Als een `ConversionSettings.json` aangeroepen bestand wordt gevonden in de invoercontainer naast het invoermodel, wordt het gebruikt om extra configuratie te bieden voor het modelconversieproces.

De inhoud van het bestand moet voldoen aan het volgende json-schema:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

Een `ConversionSettings.json` voorbeeldbestand kan zijn:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometrieparameters

* `scaling`- Deze parameter schaalt een model uniform. Schalen kan worden gebruikt om een model te laten groeien of krimpen, bijvoorbeeld om een bouwmodel op een tafelblad weer te geven. Aangezien de rendering engine verwacht dat lengtes in meters worden gespecificeerd, ontstaat een ander belangrijk gebruik van deze parameter wanneer een model in verschillende eenheden wordt gedefinieerd. Als een model bijvoorbeeld in centimeters is gedefinieerd, moet het toepassen van een schaal van 0,01 het model op de juiste grootte weergeven.
Sommige brongegevensindelingen (bijvoorbeeld .fbx) bieden een hint voor het schalen per eenheid, in welk geval de conversie het model impliciet schaalt naar metereenheden. De impliciete schaling die wordt geboden door de bronnotatie wordt toegepast bovenop de parameter schalen.
De uiteindelijke schalingfactor wordt toegepast op de geometrieen en de lokale transformaties van de scènegrafiekknooppunten. De schaling voor de transformatie van de hoofdentiteit blijft ongewijzigd.

* `recenterToOrigin`- bepaalt dat een model moet worden omgezet, zodat het selectiekader bij de oorsprong is gecentreerd.
Centreren is belangrijk als het bronmodel ver van de oorsprong wordt verplaatst, omdat in dat geval floating point precision-problemen renderingartefacten kunnen veroorzaken.

* `opaqueMaterialDefaultSidedness`- De rendering engine gaat ervan uit dat ondoorzichtige materialen dubbelzijdig zijn.
Als dat niet het beoogde gedrag is, moet deze parameter worden ingesteld op "SingleSided". Zie voor meer informatie [eenzijdige rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Materiaaloverschrijvingen

* `material-override`- Deze parameter maakt het mogelijk de verwerking van materialen aan te [passen tijdens de conversie](override-materials.md).

### <a name="color-space-parameters"></a>Parameters kleurruimte

De rendering engine verwacht dat kleurwaarden zich in de lineaire ruimte begeven.
Als een model wordt gedefinieerd met behulp van gammaruimte, moeten deze opties worden ingesteld op true.

* `gammaToLinearMaterial`- Zet materiaalkleuren om van gammaruimte naar lineaire ruimte
* `gammaToLinearVertex`- Vertexkleuren converteren van gammaruimte naar lineaire ruimte

> [!NOTE]
> Voor FBX-bestanden zijn `true` deze instellingen standaard ingesteld. Voor alle andere bestandstypen `false`is de standaardinstelling .

### <a name="scene-parameters"></a>Scèneparameters

* `sceneGraphMode`- Hiermee definieert u hoe de scènegrafiek in het bronbestand wordt geconverteerd:
  * `dynamic`(standaard): Alle objecten in het bestand worden weergegeven als [entiteiten](../../concepts/entities.md) in de API en kunnen onafhankelijk van elkaar worden getransformeerd. De knooppunthiërarchie bij runtime is identiek aan de structuur in het bronbestand.
  * `static`: Alle objecten worden in de API weergegeven, maar kunnen niet onafhankelijk worden getransformeerd.
  * `none`: De scènegrafiek wordt samengevouwen tot één object.

Elke modus heeft verschillende runtime-prestaties. In `dynamic` de modus schalen de prestatiekosten lineair met het aantal [entiteiten](../../concepts/entities.md) in de grafiek, zelfs als er geen onderdeel wordt verplaatst. Het mag alleen worden gebruikt wanneer het verplaatsen van onderdelen afzonderlijk nodig is voor de toepassing, bijvoorbeeld voor een 'explosie weergave' animatie.

De `static` modus exporteert de volledige scènegrafiek, maar delen in deze grafiek hebben een constante transformatie ten opzichte van het hoofddeel. Het hoofdknooppunt van het object kan echter nog steeds worden verplaatst, gedraaid of geschaald zonder aanzienlijke prestatiekosten. Bovendien zullen [ruimtelijke query's](../../overview/features/spatial-queries.md) afzonderlijke onderdelen retourneren en kan elk onderdeel worden gewijzigd door [middel van statusoverschrijvingen.](../../overview/features/override-hierarchical-state.md) Met deze modus is de runtime overhead per object verwaarloosbaar. Het is ideaal voor grote scènes waar u nog steeds inspectie per object nodig hebt, maar geen transformatieper object.

De `none` modus heeft de minste runtime overhead en ook iets betere laadtijden. Inspectie of transformatie van afzonderlijke objecten is niet mogelijk in deze modus. Use cases zijn bijvoorbeeld fotogrammetriemodellen die in de eerste plaats geen zinvolle scènegrafiek hebben.

> [!TIP]
> Veel toepassingen zullen meerdere modellen laden. U moet de conversieparameters voor elk model optimaliseren, afhankelijk van hoe het wordt gebruikt. Als u bijvoorbeeld het model van een auto wilt weergeven die de gebruiker uit elkaar `dynamic` wil halen en in detail wilt inspecteren, moet u deze met de modus converteren. Als u de auto echter bovendien in een showroom wilt plaatsen, `sceneGraphMode` kan `static` dat `none`model worden omgezet met in- of zelfs .

### <a name="physics-parameters"></a>Fysicaparameters

* `generateCollisionMesh`- Als u ondersteuning nodig hebt voor [ruimtelijke query's](../../overview/features/spatial-queries.md) op een model, moet deze optie worden ingeschakeld. In het ergste geval kan het maken van een botsingsgaas de conversietijd verdubbelen. Modellen met botsingsmazen doen er langer `dynamic` over om te laden en bij het gebruik van een scènegrafiek hebben ze ook een hogere runtime-prestatieoverhead. Voor algemene optimale prestaties moet u deze optie uitschakelen op alle modellen waarop u geen ruimtelijke query's nodig hebt.

### <a name="unlit-materials"></a>Onverlichte materialen

* `unlitMaterials`- Standaard geeft de conversie de voorkeur aan [PBR-materialen](../../overview/features/pbr-materials.md). Deze optie vertelt de converter om alle materialen te behandelen als [kleurmaterialen](../../overview/features/color-materials.md) in plaats daarvan. Als u gegevens hebt die al verlichting bevatten, zoals modellen die zijn gemaakt via fotogrammetrie, u met deze optie snel de juiste conversie voor alle materialen afdwingen, zonder dat elk materiaal afzonderlijk hoeft te [worden overschreven.](override-materials.md)

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Converteren van oudere FBX-formaten, met een Phong-materiaalmodel

* `fbxAssumeMetallic`- Oudere versies van het FBX-formaat definiëren hun materialen met behulp van een Phong-materiaalmodel. Het conversieproces moet afleiden hoe deze materialen in kaart brengen naar het [PBR-model](../../overview/features/pbr-materials.md)van de renderer. Meestal werkt dit goed, maar een ambiguïteit kan ontstaan wanneer een materiaal heeft geen texturen, hoge spiegelwaarden, en een niet-grijze albedo kleur. In deze omstandigheden moet de conversie kiezen tussen het prioriteren van de hoge spiegelwaarden, het definiëren van een sterk reflecterend, metaalachtig materiaal waar de albedo-kleur oplost, of het prioriteren van de albedo-kleur, het definiëren van iets als een glanzend kleurrijk plastic. Standaard gaat het conversieproces ervan uit dat zeer spiegelende waarden een metaalmateriaal impliceren in gevallen waarin ambiguïteit van toepassing is. Deze parameter kan `false` worden ingesteld om over te schakelen naar het tegenovergestelde.

### <a name="coordinate-system-overriding"></a>Coördineren systeem overschrijvend

* `axis`- Het overschrijven van coördinaten systeem-eenheid-vectoren. Standaardwaarden `["+x", "+y", "+z"]`zijn . In theorie heeft de FBX-indeling een header waarbij deze vectoren worden gedefinieerd en de conversie die informatie gebruikt om de scène te transformeren. De glTF-indeling definieert ook een vast coördinaatsysteem. In de praktijk hebben sommige activa onjuiste informatie in hun kopof zijn ze opgeslagen met een andere coördinatensysteemconventie. Met deze optie u het coördinatensysteem overschrijven om dit te compenseren. Bijvoorbeeld: `"axis" : ["+x", "+z", "-y"]` wisselt de Z-as en de Y-as uit en houdt de snelheid van het coördinatensysteem door de Y-asrichting om te keren.

### <a name="vertex-format"></a>Vertex-indeling

Het is mogelijk om het hoekpuntformaat voor een mesh aan te passen, om precisie te verhandelen voor geheugenbesparingen. Met een lagere geheugenvoetafdruk u grotere modellen laden of betere prestaties bereiken. Afhankelijk van uw gegevens kan de verkeerde indeling echter een aanzienlijke invloed hebben op de weergavekwaliteit.

> [!CAUTION]
> Het wijzigen van het hoekpuntformaat moet een laatste redmiddel zijn wanneer modellen niet meer in het geheugen passen of bij het optimaliseren voor de best mogelijke prestaties. Wijzigingen kunnen gemakkelijk rendering artefacten introduceren, zowel voor de hand liggende als subtiele. Tenzij u weet waar u op moet letten, moet u de standaardwaarde niet wijzigen.

Deze aanpassingen zijn mogelijk:

* Specifieke gegevensstromen kunnen expliciet worden opgenomen of uitgesloten.
* De nauwkeurigheid van gegevensstromen kan worden verminderd om de geheugenvoetafdruk te verminderen.

De `vertex` volgende sectie `.json` in het bestand is optioneel. Voor elk gedeelte dat niet expliciet is opgegeven, valt de conversieservice terug naar de standaardinstelling.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Door een component `NONE`te dwingen tot , is het gegarandeerd dat de output mesh niet de respectieve stroom heeft.

#### <a name="component-formats-per-vertex-stream"></a>Componentindelingen per vertexstream

Deze indelingen zijn toegestaan voor de respectieve onderdelen:

| Vertex-component | Ondersteunde indelingen (vet = standaard) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|kleur0| **8_8_8_8_UNSIGNED_NORMALIZED**, NONE |
|kleur1| 8_8_8_8_UNSIGNED_NORMALIZED, **GEEN**|
|Normale| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, GEEN |
|Tangens| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, GEEN |
|binormaal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, GEEN |
|texcoord0 texcoord0| **32_32_FLOAT**, 16_16_FLOAT, GEEN |
|texcoord1 texcoord1| **32_32_FLOAT**, 16_16_FLOAT, GEEN |

#### <a name="supported-component-formats"></a>Ondersteunde componentindelingen

De geheugenvoetafdrukken van de indelingen zijn als volgt:

| Indeling | Beschrijving | Bytes per hoekpunt |
|:-------|:------------|:---------------|
|32_32_FLOAT|tweecomponenten volledige drijvende puntprecisie|8
|16_16_FLOAT|tweecomponenten halve drijvende puntprecisie|4
|32_32_32_FLOAT|driecomponenten volledige drijvende puntprecisie|12
|16_16_16_16_FLOAT|precisie van vier componenten half zwevend punt|8
|8_8_8_8_UNSIGNED_NORMALIZED|viercomponentenbyte, genormaliseerd `[0; 1]` tot bereik|4
|8_8_8_8_SIGNED_NORMALIZED|viercomponentenbyte, genormaliseerd `[-1; 1]` tot bereik|4

#### <a name="best-practices-for-component-format-changes"></a>Aanbevolen procedures voor wijzigingen in componentindeling

* `position`: Het is zeldzaam dat een verminderde nauwkeurigheid voldoende is. **16_16_16_16_FLOAT** introduceert merkbare kwantisering artefacten, zelfs voor kleine modellen.
* `normal`, `tangent` `binormal`, : Deze waarden worden meestal samen gewijzigd. Tenzij er merkbare lichtartefacten zijn die het gevolg zijn van normale kwantisering, is er geen reden om hun nauwkeurigheid te verhogen. In sommige gevallen kunnen deze componenten echter worden ingesteld op **GEEN:**
  * `normal`, `tangent`en `binormal` zijn alleen nodig wanneer ten minste één materiaal in het model moet worden verlicht. In ARR is dit het geval wanneer een [PBR-materiaal](../../overview/features/pbr-materials.md) op elk gewenst moment op het model wordt gebruikt.
  * `tangent`en `binormal` zijn alleen nodig wanneer een van de verlichte materialen maakt gebruik van een normale kaart textuur.
* `texcoord0`, `texcoord1` : Structuurcoördinaten kunnen een verminderde nauwkeurigheid **(16_16_FLOAT)** gebruiken wanneer hun waarden in het `[0; 1]` bereik blijven en wanneer de geadresseerde texturen een maximale grootte van 2048 x 2048 pixels hebben. Als deze limieten worden overschreden, zal de kwaliteit van textuurtoewijzing lijden.

#### <a name="example"></a>Voorbeeld

Stel dat u een fotogrammetrie model, die verlichting gebakken in de texturen heeft. Alles wat nodig is om het model te renderen zijn hoekpuntposities en textuurcoördinaten.

Standaard moet de converter ervan uitgaan dat u op een bepaald moment PBR-materialen `tangent`op `binormal` een model wilt gebruiken, zodat het `normal`, en gegevens voor u genereert. Bijgevolg `position` is het geheugengebruik per hoekpunt (12 bytes) + `texcoord0` (8 `tangent` bytes) + `binormal` `normal` (4 bytes) + (4 bytes) + (4 bytes) = (4 byte) = 32 bytes. Grotere modellen van dit type kunnen gemakkelijk vele miljoenen vertices resulterend in modellen die meerdere gigabytes geheugen kunnen nemen. Dergelijke grote hoeveelheden gegevens hebben invloed op de prestaties en u zelfs zonder geheugen komen te zitten.

Wetende dat u nooit dynamische verlichting nodig op het model, en wetende dat alle textuur coördinaten zijn binnen `[0; 1]` bereik, kunt `normal`u instellen , `tangent`, en `binormal` op `NONE` en `texcoord0` op de helft precisie (`16_16_FLOAT`), wat resulteert in slechts 16 bytes per hoekpunt. Door de mesh-gegevens in de helft te halveren, u grotere modellen laden en de prestaties mogelijk verbeteren.

## <a name="typical-use-cases"></a>Typische use cases

Het vinden van goede importinstellingen voor een bepaalde use case kan een vervelend proces zijn. Aan de andere kant kunnen conversie-instellingen een aanzienlijke invloed hebben op de prestaties van runtime.

Er zijn bepaalde klassen van use cases die in aanmerking komen voor specifieke optimalisaties. Enkele voorbeelden worden hieronder gegeven.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Use case: Architecturalvisualization / grote buitenkaarten

* Dit soort scènes hebben de neiging om statisch te zijn, wat betekent dat ze geen beweegbare onderdelen nodig hebben. Dienovereenkomstig `sceneGraphMode` kan het `static` worden `none`ingesteld op of zelfs , die runtime prestaties verbetert. Met `static` de modus kan het hoofdknooppunt van de scène nog steeds worden verplaatst, gedraaid en geschaald, bijvoorbeeld om dynamisch te schakelen tussen 1:1-schaal (voor de weergave eerste persoon) en een tabelweergave.

* Wanneer u onderdelen moet verplaatsen, betekent dat meestal ook dat u ondersteuning nodig hebt voor raycasts of andere [ruimtelijke query's,](../../overview/features/spatial-queries.md)zodat u die onderdelen in de eerste plaats kiezen. Aan de andere kant, als je niet van plan bent om iets te verplaatsen, is de kans groot dat `generateCollisionMesh` je het ook niet nodig hebt om deel te nemen aan ruimtelijke query's en dus de vlag uit te schakelen. Deze switch heeft aanzienlijke invloed op de conversietijden, laadtijden en ook de kosten van de update van runtime per frame.

* Als de toepassing geen [snijvlakken](../../overview/features/cut-planes.md)gebruikt, moet de `opaqueMaterialDefaultSidedness` vlag worden uitgeschakeld. De prestatiewinst is meestal 20%-30%. Gesneden vlakken kunnen nog steeds worden gebruikt, maar er zullen geen back-faces zijn wanneer u de binnenste delen van objecten bekijkt, die er contra-intuïtief uitzien. Zie voor meer informatie [eenzijdige rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Use case: Fotogrammetrie modellen

Bij het renderen van fotogrammetriemodellen is er meestal geen `sceneGraphMode` behoefte `none`aan een scènegrafiek, dus u de. Aangezien deze modellen zelden een complexe scènegrafiek bevatten om te beginnen, moet de impact van deze optie echter onbeduidend zijn.

Omdat verlichting al in de texturen is gebakken, is er geen dynamische verlichting nodig. Daarom:

* Stel `unlitMaterials` de `true` vlag in om alle materialen om te zetten in onverlichte [kleurmaterialen.](../../overview/features/color-materials.md)
* Verwijder onnodige gegevens uit de vertex-indeling. Zie het [voorbeeld](#example) hierboven.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Use case: Visualisatie van compacte machines, etc.

In deze use cases hebben de modellen vaak een zeer hoog detail binnen een klein volume. De renderer is zwaar geoptimaliseerd om dergelijke gevallen goed te behandelen. De meeste optimalisaties die in de vorige use case worden genoemd, zijn hier echter niet van toepassing:

* Afzonderlijke onderdelen moeten selecteerbaar en verplaatsbaar zijn, zodat de `sceneGraphMode` moet worden overgelaten aan `dynamic`.
* Ray casts zijn meestal een integraal onderdeel van de toepassing, dus botsing mazen moeten worden gegenereerd.
* Gesneden vliegtuigen zien `opaqueMaterialDefaultSidedness` er beter uit met de vlag ingeschakeld.

## <a name="next-steps"></a>Volgende stappen

* [Modelconversie](model-conversion.md)
* [Kleurmaterialen](../../overview/features/color-materials.md)
* [PBR-materialen](../../overview/features/pbr-materials.md)
* [Materialen overschrijven tijdens modelconversie](override-materials.md)
