---
title: Aangepaste modellen
titleSuffix: Azure Digital Twins
description: Begrijp hoe Azure Digital Apparaatdubbels de door de gebruiker gedefinieerde modellen gebruikt om entiteiten in uw omgeving te beschrijven.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 30bf5d3eaa034c033cf32e26c3229a028f036313
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84613136"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Meer informatie over dubbele modellen in azure Digital Apparaatdubbels

Een belang rijk kenmerk van Azure Digital Apparaatdubbels is de mogelijkheid om uw eigen woorden lijst te definiëren en uw dubbele grafiek te bouwen in de zelfgedefinieerde voor waarden van uw bedrijf. Deze mogelijkheid wordt gegeven via door de gebruiker gedefinieerde **modellen**. U kunt modellen beschouwen als de zelfstandige naam woorden in een beschrijving van uw wereld. 

Een model is vergelijkbaar met een **klasse** in een object georiënteerde programmeer taal en definieert een gegevensshape voor een bepaald concept in uw echte werk omgeving. Modellen hebben namen (zoals *room* of *temperatuur sensor*) en bevatten elementen zoals eigenschappen, telemetrie/gebeurtenissen en opdrachten die beschrijven wat dit type entiteit in uw omgeving kan doen. Later gaat u deze modellen gebruiken om [**digitale apparaatdubbels**](concepts-twins-graph.md) te maken die specifieke entiteiten vertegenwoordigen die aan deze type beschrijving voldoen.

Modellen worden geschreven met behulp van de op JSON gebaseerde **Digital-taal voor dubbele definitie (DTDL)**.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>Digital-dubbele-definitie taal (DTDL) voor het schrijven van modellen

Modellen voor Azure Digital Apparaatdubbels worden gedefinieerd met behulp van de Digital Apparaatdubbels Definition Language (DTDL). DTDL is gebaseerd op JSON-LD en is onafhankelijk van programmeer taal. DTDL is niet exclusief voor Azure Digital Apparaatdubbels, maar wordt ook gebruikt voor het weer geven van apparaatgegevens in andere IoT-Services, zoals [IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md). Azure Digital Apparaatdubbels maakt gebruik van DTDL *versie 2*.

> [!TIP] 
> Niet alle services die gebruikmaken van DTDL, implementeren exact dezelfde functies van DTDL. IoT Plug en Play maakt bijvoorbeeld geen gebruik van de DTDL-functies die voor grafieken gelden, terwijl Azure Digital Apparaatdubbels momenteel geen DTDL-opdrachten implementeert. Voor meer informatie over de DTDL-functies die specifiek zijn voor Azure Digital Apparaatdubbels raadpleegt u de sectie verderop in dit artikel over de [Implementatie Details van Azure Digital APPARAATDUBBELS DTDL](#azure-digital-twins-dtdl-implementation-specifics).

Voor meer informatie over DTDL in het algemeen raadpleegt u de documentatie van spec in GitHub: [Digital Apparaatdubbels Definition Language (DTDL)-versie 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>Elementen van een model

Binnen een model definitie is het code-item op het hoogste niveau een **Interface**. Hiermee wordt het volledige model ingekapseld en de rest van het model gedefinieerd in de interface. 

Een DTDL-model interface kan nul, één of veel van de volgende velden bevatten:
* **Eigenschap** -eigenschappen zijn gegevens velden die de status van een entiteit vertegenwoordigen (zoals de eigenschappen in veel object-georiënteerde programmeer talen). In tegens telling tot telemetrie, een tijdgebonden gegevens gebeurtenis, hebben eigenschappen een back-up van de opslag en kunnen ze op elk gewenst moment worden gelezen.
* **Telemetrie** -telemetrie-velden vertegenwoordigen metingen of gebeurtenissen en worden vaak gebruikt om de leesingen van de sensor te beschrijven. Telemetrie wordt niet opgeslagen op een digitale dubbele; het lijkt meer op een stroom aan gegevens gebeurtenissen die ergens kunnen worden verzonden. 
* **Onderdeel** -onderdelen bieden u de mogelijkheid om uw model interface als een assembly van andere interfaces te maken, als u dat wilt. Een voor beeld van een component is een *frontCamera* -interface (en een andere onderdeel interface *backCamera*) die worden gebruikt voor het definiëren van een model voor een *telefoon*. U moet eerst een interface voor *frontCamera* definiëren alsof het een eigen model is, en vervolgens kunt u hiernaar verwijzen bij het definiëren van de *telefoon*.

    Gebruik een onderdeel om iets te beschrijven dat een integraal onderdeel is van uw oplossing, maar waarvoor geen afzonderlijke identiteit nodig is, en dat u niet afzonderlijk hoeft te maken, verwijderen of opnieuw wilt rangschikken in het dubbele diagram. Als u wilt dat entiteiten onafhankelijk van elkaar aanwezig zijn in de dubbele grafiek, vertegenwoordigen ze als afzonderlijke digitale apparaatdubbels van verschillende modellen, verbonden door *relaties* (zie volgende opsommings teken).
    
    >[!TIP] 
    >Onderdelen kunnen ook worden gebruikt voor organisaties om sets van gerelateerde eigenschappen binnen een model interface te groeperen. In dit geval kunt u elk onderdeel beschouwen als een naam ruimte of ' folder ' in de-interface.
* **Relatie** -relaties bieden u de mogelijkheid om te zien hoe een digitale twee kunnen worden betrokken bij andere digitale apparaatdubbels. Relaties kunnen verschillende semantische betekenissen vertegenwoordigen, zoals *contains* ("vloer bevat room"), *koelen* ("HVAC Cool Room"), *isBilledTo* ("compressor" wordt gefactureerd voor gebruiker "), enzovoort. Met relaties kan de oplossing een grafiek van gerelateerde entiteiten bieden.

> [!NOTE]
> De specificatie voor DTDL definieert ook **opdrachten**. Dit zijn methoden die kunnen worden uitgevoerd op een digitale twee (zoals een reset-opdracht of een opdracht om een ventilator in of uit te scha kelen). *Opdrachten worden momenteel echter niet ondersteund in azure Digital apparaatdubbels.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Specifieke Azure Digital Apparaatdubbels DTDL-implementaties

Een DTDL-model om compatibel te zijn met Azure Digital Apparaatdubbels, moet aan deze vereisten voldoen.

* Alle DTDL-elementen op het hoogste niveau in een model moeten van het type *Interface*zijn. Dit komt doordat Azure Digital Apparaatdubbels model-Api's JSON-objecten kunnen ontvangen die een interface of een matrix van interfaces vertegenwoordigen. Als gevolg hiervan zijn geen andere DTDL-element typen toegestaan op het hoogste niveau.
* DTDL voor Azure Digital Apparaatdubbels mogen geen *opdrachten*definiëren.
* Azure Digital Apparaatdubbels staat slechts één niveau van geneste onderdelen toe. Dit betekent dat een interface die als onderdeel wordt gebruikt, zelf geen onderdelen kan hebben. 
* Interfaces kunnen niet in line worden gedefinieerd binnen andere DTDL-interfaces; ze moeten worden gedefinieerd als afzonderlijke entiteiten op het hoogste niveau met hun eigen Id's. Wanneer een andere interface die interface wil toevoegen als een onderdeel of via overname, kan deze verwijzen naar de ID.

## <a name="example-model-code"></a>Voorbeeld model code

Dubbele type modellen kunnen worden geschreven in elke tekst editor. De DTDL-taal volgt de JSON-syntaxis, dus u moet modellen met de extensie *. json*opslaan. Door gebruik te maken van de JSON-extensie biedt veel Program meren-tekst editors de mogelijkheid om basis syntaxis controles uit te voeren en te markeren voor uw DTDL-documenten. Er is ook een [DTDL-extensie](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) beschikbaar voor [Visual Studio code](https://code.visualstudio.com/).

Hier volgt een voor beeld van een typisch model, geschreven als een DTDL-interface. Het model beschrijft plan eten, elk met een naam, massa en een Tempe ratuur. De planeet kunnen manen als satelliet hebben en kan Craters bevatten.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

De velden van het model zijn:

| Veld | Beschrijving |
| --- | --- |
| `@id` | Een id voor het model. Moet de indeling hebben `dtmi:<domain>:<unique model identifier>;<model version number>` . |
| `@type` | Hiermee wordt het type informatie aangegeven dat wordt beschreven. Voor een interface is het type *Interface*. |
| `@context` | Hiermee stelt u de [context](https://niem.github.io/json/reference/json-ld/context/) voor het JSON-document. Modellen moeten worden gebruikt `dtmi:dtdl:context;2` . |
| `displayName` | Beschrijving Hiermee kunt u het model een beschrijvende naam geven, indien gewenst. |
| `contents` | Alle overige Interface gegevens worden hier geplaatst, als een matrix met kenmerk definities. Elk kenmerk moet een `@type` (*eigenschap*, *telemetrie*, *opdracht*, *relatie*of *onderdeel*) bevatten om de sortering van interface gegevens te identificeren die hierin wordt beschreven, en vervolgens een set eigenschappen die het werkelijke kenmerk definiëren (bijvoorbeeld `name` en `schema` om een *eigenschap*te definiëren). |

> [!NOTE]
> Houd er rekening mee dat de component interface (*Crater* in dit voor beeld) is gedefinieerd in dezelfde matrix als de interface die gebruikmaakt van IT (*planeet*). Onderdelen moeten op deze manier worden gedefinieerd in API-aanroepen om de interface te vinden.

### <a name="possible-schemas"></a>Mogelijke schema's

Volgens DTDL kan het schema voor *eigenschaps* -en *telemetrie* -kenmerken van de standaard primitieve typen,,, `integer` en en `double` `string` `Boolean` andere typen, zoals `DateTime` en `Duration` , zijn. 

Naast primitieve typen kunnen *eigenschaps* -en *telemetrie* -velden de volgende complexe typen hebben:
* `Object`
* `Map`
* `Enum`

*Telemetrie* -velden ondersteunen ook `Array` .

### <a name="model-inheritance"></a>Model overname

Soms wilt u mogelijk een model specialiseren. Het kan bijvoorbeeld nuttig zijn om een algemene model *kamer*te hebben, en speciale varianten *ConferenceRoom* en *Gym*. Voor het expliciet aanduiden van specialisatie biedt DTDL ondersteuning voor overname: interfaces kunnen overnemen van een of meer andere interfaces. 

In het volgende voor beeld wordt het *planeet* model van het vorige DTDL-voor beeld opnieuw beschouwd als een subtype van een groter *CelestialBody* -model. Het bovenliggende model wordt eerst gedefinieerd en vervolgens wordt het onderliggende model op basis van het veld samengesteld `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

In dit voor beeld draagt *CelestialBody* bij aan een naam, massa en een temperatuur voor *planeet*. De `extends` sectie is een interface naam of een matrix met interface namen (waardoor de uitbreidings interface kan overnemen van meerdere bovenliggende modellen, indien gewenst).

Zodra overname is toegepast, worden alle eigenschappen van de volledige overname keten beschikbaar gemaakt door de uitbrei ding van de interface.

De uitbreidende interface kan geen van de definities van de bovenliggende interfaces wijzigen; Er kan alleen worden toegevoegd. Het is ook niet mogelijk een functie opnieuw te definiëren die al is gedefinieerd in een van de bovenliggende interfaces (zelfs als de mogelijkheden zijn gedefinieerd om hetzelfde te zijn). Als een bovenliggende interface bijvoorbeeld een `double` eigenschaps *massa*definieert, kan de uitbrei ding van de interface geen declaratie van *massa*bevatten, zelfs als deze ook a `double` .

## <a name="validating-models"></a>Modellen valideren

Er is een voor beeld beschikbaar voor het valideren van model documenten om ervoor te zorgen dat de DTDL geldig is. Het is gebouwd op de DTDL parser-bibliotheek en is taal-neutraal. Vind het hier: [DTDL validator](https://github.com/Azure-Samples/DTDL-Validator)-voor beeld.

Als u meer informatie wilt over de parser-bibliotheek, met inbegrip van een voor beeld van hoe u deze kunt gebruiken, raadpleegt u [procedures: modellen parseren en valideren](how-to-use-parser.md).

## <a name="next-steps"></a>Volgende stappen

Zie modellen beheren met de DigitalTwinsModels-Api's:
* [Instructies: een twee ledig model beheren](how-to-manage-model.md)

Of leer hoe digitale apparaatdubbels worden gemaakt op basis van modellen:
* [Concepten: Digital apparaatdubbels en het dubbele diagram](concepts-twins-graph.md)

