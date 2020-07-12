---
title: Pakket vereisten in Azure Maps Maker tekenen
description: Meer informatie over de vereisten voor het teken pakket voor het converteren van uw ontwerp bestanden voor de locatie om gegevens toe te wijzen met behulp van de Azure Maps conversie service
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1ba9edba97ce89cede54287076e50eb587af10f3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242471"
---
# <a name="drawing-package-requirements"></a>Vereisten voor tekenpakketten

Met de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) kunt u geüploade teken pakketten naar kaart gegevens converteren. In dit artikel worden de teken pakket vereisten voor de conversie-API beschreven. Als u een voorbeeld pakket wilt weer geven, kunt u het voorbeeld [teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples)downloaden.

## <a name="prerequisites"></a>Vereisten

Het teken pakket bevat tekeningen die zijn opgeslagen in de DWG-indeling, de systeem eigen bestands indeling voor de AutoCAD®-software van auto Desk, een [handels merk van auto Desk, Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

U kunt kiezen voor alle CAD-software om de tekeningen in het teken pakket te maken.  

De [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) converteert het tekening pakket naar kaart gegevens.  De conversie service is ontwikkeld en getest met de AutoCAD DWG-bestands indeling. `AC1032`is de interne indelings versie voor de DWG-bestanden. U wordt geadviseerd om `AC1032` de interne versie van de DWG-bestands indeling te selecteren.  

Woorden lijst met termen die in dit document worden gebruikt.

| Term  | Definitie |
|:-------|:------------|
| Laag | Een AutoCAD DWG-laag.|
| Niveau | Een gebied van een gebouw bij een ingestelde uitbrei ding. Bijvoorbeeld de basis van een gebouw. |
| XREF  |Een bestand in AutoCAD DWG-bestands indeling (. DWG) dat is gekoppeld aan de primaire tekening als externe verwijzing.  |
| Functie | Een object dat een geometrie combineert met aanvullende meta gegevens. |
| Functie klassen | Een veelvoorkomende blauw druk voor functies. Een eenheid is bijvoorbeeld een functie klasse en een kantoor is een functie. |

## <a name="drawing-package-structure"></a>Structuur van tekening pakket

Een teken pakket is een zip-archief dat de volgende bestanden bevat:

* DWG-bestanden in AutoCAD DWG-bestands indeling.
* Een _manifest.jsin_ het bestand voor één faciliteit.

De DWG-bestanden kunnen op een wille keurige manier in de map worden ingedeeld, maar het manifest bestand moet zich in de hoofdmap van de map bevinden. De map moet zijn ingepakt in één archief bestand met de extensie. zip. De volgende secties beschrijven de vereisten voor de DWG-bestanden, het manifest bestand en de inhoud van deze bestanden.  

## <a name="dwg-files-requirements"></a>Vereisten voor DWG-bestanden

Er is één DWG-bestand vereist voor elk niveau van de faciliteit. De gegevens van het niveau moeten zich in één DWG-bestand bevinden. Alle externe verwijzingen (_xrefs_) moeten aan de bovenliggende tekening worden gebonden. Daarnaast elk DWG-bestand:

* U moet de _buiten_ -en _eenheids_ lagen definiëren. U kunt eventueel de volgende optionele lagen definiëren: _wand_, _deur_, _UnitLabel_, _zone_en _ZoneLabel_.
* Mag geen functies van meerdere niveaus bevatten.
* Mag geen functies van meerdere faciliteiten bevatten.

De [Azure Maps-conversie service](https://docs.microsoft.com/rest/api/maps/conversion) kan de volgende functie klassen uit een DWG-bestand extra heren:

* Niveaus
* Eenheden
* Zones
* Openingen
* Lijnen
* Verticale binnendringingen

Alle conversie taken resulteren in een minimale set standaard categorieën: room, structuur. Wall, openen. deur, zone en faciliteit. Aanvullende categorieën zijn voor elke categorie naam waarnaar wordt verwezen door objecten.  

Een DWG-laag moet functies van één klasse bevatten. Klassen mogen geen laag delen. Eenheden en wanden kunnen bijvoorbeeld geen laag delen.

DWG-lagen moeten ook voldoen aan de volgende criteria:

* De oorsprong van tekeningen voor alle DWG-bestanden moeten worden uitgelijnd op dezelfde breedte graad en lengte graad.
* Elk niveau moet dezelfde richting hebben als de andere niveaus.
* Niet-overlappende veelhoeken worden automatisch hersteld en er wordt een waarschuwing gegenereerd door de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) . Het is raadzaam om de gerepareerde resultaten hand matig te controleren, omdat deze mogelijk niet overeenkomen met de verwachte resultaten.

Alle laag entiteiten moeten een van de volgende typen zijn: lijn, poly lijn, veelhoek, cirkel vormige boog, cirkel, tekst (enkele regel). Alle andere entiteits typen worden genegeerd.

De onderstaande tabel bevat een overzicht van de ondersteunde entiteits typen en ondersteunde functies voor elke laag. Als een laag entiteits typen bevat die niet worden ondersteund, worden deze entiteiten genegeerd door de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) .  

| Laag | Entiteitstypen | Functies |
| :----- | :-------------------| :-------
| [Postzegel](#exterior-layer) | Veelhoek, poly lijn (gesloten), cirkel | Niveaus
| [Eenheid](#unit-layer) |  Veelhoek, poly lijn (gesloten), cirkel | Verticale binnendringingen, eenheden
| [Muren](#wall-layer)  | Veelhoek, poly lijn (gesloten), cirkel | Niet van toepassing. Zie de laag met de [wand](#wall-layer)voor meer informatie.
| [Door](#door-layer) | Veelhoek, poly lijn, lijn, CircularArc, cirkel | Openingen
| [Zone](#zone-layer) | Veelhoek, poly lijn (gesloten), cirkel | Zone
| [UnitLabel](#unitlabel-layer) | Tekst (één regel) | Niet van toepassing. Deze laag kan alleen eigenschappen toevoegen aan de eenheids functies van de laag eenheden. Zie de [UnitLabel-laag](#unitlabel-layer)voor meer informatie.
| [ZoneLabel](#zonelabel-layer) | Tekst (één regel) | Niet van toepassing. Deze laag kan alleen eigenschappen toevoegen aan zone functies van de ZonesLayer. Zie de [ZoneLabel-laag](#zonelabel-layer) voor meer informatie.

In de volgende secties worden de vereisten voor elke laag gedetailleerd beschreven.

### <a name="exterior-layer"></a>Buiten-laag

Het DWG-bestand voor elk niveau moet een laag bevatten om de omtrek van dat niveau te definiëren. Deze laag wordt aangeduid als de buitenste laag. Als een faciliteit bijvoorbeeld twee niveaus bevat, moet deze twee DWG-bestanden hebben, met een buitenste laag voor elk bestand.

Nu ziet u hoeveel entiteits tekeningen zich in de buiten-laag bevinden, de [resulterende faciliteit gegevensset](tutorial-creator-indoor-maps.md#create-a-feature-stateset) bevat slechts **één** niveau functie voor elk DWG-bestand. Aanvullend:

* Buitens moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel.

* Buiten kant kan overlappen, maar wordt in één geometrie opgelost.

Als de laag meerdere overlappende polylijnen bevat, worden de polylinen opgelost in een functie met één niveau. Als de laag meerdere non_overlapping polylinen bevat, heeft de resulterende niveau functie echter een multi-veelhoekige weer gave.

Een voor beeld van de buitenste laag kan worden gezien als de OVERZICHTSKOP in het [voorbeeld tekening pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Eenheids laag

In het DWG-bestand voor elk niveau moet een laag met eenheden worden gedefinieerd.  Eenheden zijn navigeerbaar ruimten in het gebouw, zoals kant oren, hal, traps en liften. De laag eenheden moet voldoen aan de volgende vereisten:

* Eenheden moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel.
* Eenheden moeten binnen de grenzen van de buitenste omtrek van de faciliteit vallen.
* Eenheden mogen niet gedeeltelijk elkaar overlappen.
* Eenheden mogen geen zelf-overlappende geometrie bevatten.

 Geef een eenheid een naam door een tekst object te maken in de laag _unitLabel_ en plaats vervolgens het object binnen de grenzen van de eenheid. Zie de [UnitLabel-laag](#unitlabel-layer)voor meer informatie.

Een voor beeld van de laag met eenheden kan worden gezien als de laag eenheden in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Laag van wand

Het DWG-bestand voor elk niveau kan een laag bevatten waarin de fysieke gebieden van wanden, kolommen en andere bouw structuren worden gedefinieerd.

* Wanden moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel.
* De laag/lagen van de wand mogen alleen geometrie bevatten die wordt geïnterpreteerd als bouw structuur.

Een voor beeld van de laag wanden kan worden gezien als de laag met de WANDen in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Laag van deur

U kunt een DWG-laag met deuren bevatten. Elke deur moet de rand van een eenheid van de laag van de eenheid overlappen.

Openingen van deuren in een Azure Maps-gegevensset worden weer gegeven als een segment met één lijn dat meerdere eenheids grenzen overlapt. De volgende stappen worden uitgevoerd om de geometrie in de laag te converteren om de functies in een gegevensset te openen.

![Stappen voor het genereren van openingen](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zone laag

Het DWG-bestand voor elk niveau kan een zone-laag bevatten die de fysieke gebieden van zones definieert. Een zone kan een lege ruimte of een back yard zijn.

* Zones moeten worden getekend als veelhoek, poly lijn (gesloten), cirkel.
* Zones kunnen elkaar overlappen.
* Zones kunnen binnen of buiten de buitenste omtrek van de faciliteit vallen.

Noem een zone door een tekst object te maken in de _zoneLabel_ -laag en het tekst object binnen de grenzen van de zone te plaatsen. Zie [ZoneLabel Layer](#zonelabel-layer)voor meer informatie.

Een voor beeld van de laag zones kan worden gezien als de laag ZONES in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>UnitLabel-laag

Het DWG-bestand voor elk niveau kan een label-laag voor de eenheid bevatten. De label-laag eenheid voegt een naam eigenschap toe aan eenheden die zijn geëxtraheerd uit de laag van de eenheid. Voor eenheden met een naam eigenschap kunnen aanvullende details worden opgegeven in het manifest bestand.

* Eenheidslabels moeten tekst entiteiten van één regel zijn.
* Eenheidslabels moeten binnen de grenzen van hun eenheid vallen.
* Eenheden mogen geen meerdere tekst entiteiten bevatten in de laag met eenheidslabels.

Een voor beeld van de laag UnitLabel kan worden gezien als de laag UNITLABELS in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel-laag

Het DWG-bestand voor elk niveau kan een zone label laag bevatten. Deze laag voegt een naam eigenschap toe aan zones die worden geëxtraheerd uit de laag van de zone. In zones met een naam eigenschap kunnen aanvullende details worden opgegeven in het manifest bestand.

* Labels voor zones moeten tekst entiteiten van één regel zijn.
* Labels voor zones moeten binnen de grenzen van hun zone vallen.
* Zones mogen geen meerdere tekst entiteiten in de laag met zone labels bevatten.

Een voor beeld van de laag Zonelabel kan worden gezien als de laag ZONELABELS in het [voorbeeld teken pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Vereisten voor manifest bestand

De map zip moet een manifest bestand op het hoofd niveau van de map bevatten en het bestand moet de naam **manifest.jsop**hebben. Hierin worden de DWG-bestanden beschreven zodat de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) de inhoud kan parseren. Alleen de bestanden die door het manifest worden geïdentificeerd, worden opgenomen in de opname. Bestanden die zich in de map zip bevinden, maar niet op de juiste manier worden vermeld in het manifest, worden genegeerd.

De bestands paden in het **buildingLevels** -object van het manifest bestand moeten relatief zijn ten opzichte van de hoofdmap van de map zip. De naam van het DWG-bestand moet exact overeenkomen met de naam van het niveau van de faciliteit. Zo zou een DWG-bestand voor het niveau ' Basement ' bijvoorbeeld ' basement. DWG ' zijn. Een DWG-bestand voor niveau 2 krijgt de naam level_2. DWG. Gebruik een onderstrepings teken als de naam van uw niveau een spatie heeft.

Hoewel er vereisten gelden wanneer u de manifest-objecten gebruikt, zijn niet alle objecten vereist. In de volgende tabel ziet u de vereiste en optionele objecten voor versie 1,1 van de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion).

| Object | Vereist | Beschrijving |
| :----- | :------- | :------- |
| versie | true |Schema versie van manifest. Op dit moment wordt alleen versie 1,1 ondersteund.|
| directoryInfo | true | Geeft een overzicht van de geografische locatie-en contact gegevens. Het kan ook worden gebruikt voor een overzicht van de geografische en contact gegevens van een inzittende. |
| buildingLevels | true | Hiermee geeft u de niveaus van de gebouwen en de bestanden die het ontwerp van de niveaus bevatten. |
| georeferentie | true | Bevat numerieke geografische gegevens voor de faciliteit tekening. |
| dwgLayers | true | Een lijst met de namen van de lagen en elke laag bevat de namen van de eigen functies. |
| unitProperties | false | Kan worden gebruikt om aanvullende meta gegevens voor de onderdelen van de eenheid in te voegen. |
| zoneProperties | false | Kan worden gebruikt om aanvullende meta gegevens in te voegen voor de zone-functies. |

In de volgende secties worden de vereisten voor elk object gedetailleerd beschreven.

### <a name="directoryinfo"></a>directoryInfo

| Eigenschap  | type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
| naam      | tekenreeks | true   |  De naam van het gebouw. |
| streetAddress|    tekenreeks |    false    | Het adres van het gebouw. |
|eenheid     | tekenreeks    |  false    |  Eenheid in gebouw. |
| plaats |    tekenreeks |    false |    De naam van een gebied, groep of regio. Bijvoorbeeld "overlake" of "Central District". De lokale locatie maakt geen deel uit van het post adres. |
| adminDivisions |    JSON-matrix met teken reeksen |    false     | Een matrix met adres ontwerpen (land, staat, plaats) of (land, prefectuur, stad, stad). Gebruik ISO 3166-land codes en ISO 3166-2 staat/regio codes. |
| Code |    tekenreeks    | false    | De e-mail BIC-code. |
| hoursOfOperation |    tekenreeks |     false | Voldoet aan de [OSM Openings uren](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) -indeling. |
| telefoon    | tekenreeks |    false |    Het telefoon nummer dat aan het gebouw is gekoppeld. De land code moet worden meegenomen. |
| website    | tekenreeks |    false    | De website die aan het gebouw is gekoppeld. M beginnen met http of https. |
| niet-openbaar |    booleaans    | false | Vlag waarmee wordt aangegeven of het gebouw open is. |
| anchorLatitude | numeriek |    false | Breedte graad van een faciliteit anker (punaise). |
| anchorLongitude | numeriek |    false | Lengte graad van een faciliteit anker (punaise). |
| anchorHeightAboveSeaLevel  | numeriek | false | Hoogte van de grond vloer van de faciliteit boven Sea-niveau, in meters. |
| defaultLevelVerticalExtent | numeriek | false | De standaard hoogte (breedte) van een niveau van deze faciliteit dat moet worden gebruikt wanneer een niveau `verticalExtent` niet is gedefinieerd. |

### <a name="buildinglevels"></a>buildingLevels

Het `buildingLevels` object bevat een JSON-matrix met de niveaus van gebouwen.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|levelName    |tekenreeks    |true |    Beschrijvende niveau naam. Bijvoorbeeld: Floor 1, lobby, Blue parkeren, Basement, enzovoort.|
|rang telwoord | geheel getal |    true | Rang nummer wordt gebruikt om de verticale volg orde van niveaus te bepalen. Elke faciliteit moet een niveau hebben met een rang telwoord van 0. |
|heightAboveFacilityAnchor | numeriek | false |    Niveau hoogte boven het anker in meters. |
| verticalExtent | numeriek | false | De vloer tot de plafond hoogte (breedte) van het niveau in meters. |
|bestandsnaam |    tekenreeks |    true |    Bestandssysteempad naar het bestands systeem van de CAD-tekening voor een gebouw niveau. Deze moet relatief zijn ten opzichte van de hoofdmap van het zip-bestand van het gebouw. |

### <a name="georeference"></a>georeferentie

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|lat    | numeriek |    true |    Decimale weer gave van graden met een breedte graad op de oorspronkelijke positie van de tekening. De oorsprongs coördinaten moeten zich in WGS84 Web Mercator () bekomen `EPSG:3857` .|
|Lon    |numeriek|    true|    Decimale weer gave van graden met de lengte van de faciliteit tekening. De oorsprongs coördinaten moeten zich in WGS84 Web Mercator () bekomen `EPSG:3857` . |
|hoek|    numeriek|    true|   De hoek rechtsom, in graden, tussen True en de verticale as (Y) van de tekening.   |

### <a name="dwglayers"></a>dwgLayers

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|postzegel    |Matrix van tekenreeksen|    true|    Namen van de lagen die het buitenste bouw profiel definiëren.|
|eenheid|    Matrix van tekenreeksen|    true|    Namen van lagen waarmee eenheden worden gedefinieerd.|
|muren|    Matrix van tekenreeksen    |false|    Namen van lagen waarmee wanden worden gedefinieerd.|
|deur    |Matrix van tekenreeksen|    false   | Namen van lagen waarmee deuren worden gedefinieerd.|
|unitLabel    |Matrix van tekenreeksen|    false    |Namen van de lagen die de namen van eenheden definiëren.|
|zone | Matrix van tekenreeksen    | false    | Namen van lagen waarmee zones worden gedefinieerd.|
|zoneLabel | Matrix van tekenreeksen |     false |    Namen van een of meer lagen die de namen van zones bepalen.|

### <a name="unitproperties"></a>unitProperties

Het `unitProperties` object bevat een JSON-matrix met de eigenschappen van de eenheid.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|eenheids    |tekenreeks    |true    |De naam van de eenheid die aan deze record moet worden gekoppeld `unitProperty` . Deze record is alleen geldig wanneer een label overeenkomst `unitName` wordt gevonden in de `unitLabel` laag (len). |
|categoryName|    tekenreeks|    false    |Categorie naam. Raadpleeg de [categorie](https://aka.ms/pa-indoor-spacecategories)voor een volledige lijst met categorieën. |
|navigableBy| Matrix van tekenreeksen |    false    |Hiermee worden de typen navigatie agenten aangegeven die de eenheid kunnen passeren. Bijvoorbeeld ' voetgangers '. Met deze eigenschap wordt de wayfinding-functionaliteit op de hoogte gesteld.  De toegestane waarden zijn `pedestrian` ,,, `wheelchair` `machine` `bicycle` , `automobile` , `hiredAuto` , `bus` , `railcar` , `emergency` , `ferry` , `boat` en `disallowed` .|
|routeThroughBehavior|    tekenreeks|    false    |Het gedrag van de route voor de eenheid. De toegestane waarden zijn `disallowed` , `allowed` en `preferred` . De standaard waarde is `allowed` .|
|inzittenden    |Matrix van directoryInfo-objecten |false    |De lijst met inzittenden voor de eenheid. |
|nameAlt|    tekenreeks|    false|    De alternatieve naam van de eenheid. |
|nameSubtitle|    tekenreeks    |false|    Subtitel van de eenheid. |
|addressRoomNumber|    tekenreeks|    false|    Kamer/eenheid/Appartement/Suite nummer van de eenheid.|
|verticalPenetrationCategory|    tekenreeks|    false| Als deze eigenschap is gedefinieerd, is de resulterende functie een verticale indringing (VRT) in plaats van een eenheid. VRTs kan worden gebruikt om te navigeren naar andere VRT-functies in de bovenstaande niveaus. Verticale indringing is een [categorie](https://aka.ms/pa-indoor-spacecategories) naam. Als deze eigenschap is gedefinieerd, wordt de eigenschap categoryName vervangen door verticalPenetrationCategory. |
|verticalPenetrationDirection|    tekenreeks|    false    |Als `verticalPenetrationCategory` is gedefinieerd, definieert u eventueel de geldige reis richting. De toegestane waarden zijn `lowToHigh` , `highToLow` , en `both` `closed` . De standaard waarde is `both` .|
| niet-openbaar | booleaans | false | Hiermee wordt aangegeven of de eenheid open is voor het publiek. |
| isRoutable | booleaans | false | Als deze instelling `false` is ingesteld op, kan eenheid niet worden genavigeerd naar of via. De standaard waarde is `true` . |
| isOpenArea | booleaans | false | Hiermee kan de navigatie agent de eenheid invoeren zonder dat er een openings koppeling met de eenheid nodig is. Deze waarde is standaard ingesteld op `true` voor eenheden zonder openingen. `false` voor eenheden met openingen.  Hand matig `isOpenArea` instellen `false` op een eenheid zonder openingen resulteert in een waarschuwing. Dit komt doordat de resulterende eenheid niet bereikbaar is voor een navigatie agent.|

### <a name="the-zoneproperties-object"></a>Het zoneProperties-object

Het `zoneProperties` object bevat een JSON-matrix met zone-eigenschappen.

| Eigenschap  | Type | Vereist | Beschrijving |
|-----------|------|----------|-------------|
|zone naam        |tekenreeks    |true    |De naam van de zone die aan de record moet worden gekoppeld `zoneProperty` . Deze record is alleen geldig wanneer een label overeenkomst `zoneName` wordt gevonden in de `zoneLabel` laag van de zone.  |
|categoryName|    tekenreeks|    false    |Categorie naam. Raadpleeg de [categorie](https://aka.ms/pa-indoor-spacecategories)voor een volledige lijst met categorieën. |
|zoneNameAlt|    tekenreeks|    false    |Alternatieve naam van de zone.  |
|zoneNameSubtitle|    tekenreeks |    false    |Ondertitel van de zone. |
|zoneSetId|    tekenreeks |    false    | Stel ID in om een relatie tussen meerdere zones tot stand te brengen, zodat deze kunnen worden opgevraagd of als groep kan worden geselecteerd. Bijvoorbeeld zones die meerdere niveaus beslaan. |

### <a name="sample-drawing-package-manifest"></a>Voor beeld van teken pakket manifest

Hieronder vindt u een voor beeld van een manifest bestand voor het voorbeeld teken pakket. Als u het hele pakket wilt downloaden, klikt u op voor [beeld tekening pakket](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Manifest bestand

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonWheelchairAccessible": false, 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

Als uw teken pakket aan de vereisten voldoet, kunt u de [Azure Maps conversie service](https://docs.microsoft.com/rest/api/maps/conversion) gebruiken om het pakket te converteren naar een kaart gegevensset. Vervolgens kunt u de gegevensset gebruiken om een binnenste kaart te genereren met behulp van de module kaarten. Lees de volgende artikelen voor meer informatie over het gebruik van de module over kaarten:

> [!div class="nextstepaction"]
>[Maker voor kaarten in de binnenste](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Zelf studie: een kaart maken voor een koppeling binnen](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamische stijlen voor kaarten in de binnenste](indoor-map-dynamic-styling.md)
