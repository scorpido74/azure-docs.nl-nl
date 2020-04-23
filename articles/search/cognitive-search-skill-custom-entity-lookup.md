---
title: Cognitieve zoekvaardigheid voor aangepaste entiteiten opzoeken
titleSuffix: Azure Cognitive Search
description: Verschillende aangepaste entiteiten uit tekst halen in een azure cognitive search cognitive search pipeline. Deze vaardigheid is momenteel in openbare preview.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 3659070d4ffd4346a8827d2748e67db436fc15b3
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085736"
---
#     <a name="custom-entity-lookup-cognitive-skill-preview"></a>Cognitieve vaardigheid voor het opzoeken van aangepaste entiteiten (voorbeeld)

> [!IMPORTANT] 
> Deze vaardigheid is momenteel in openbare preview. Preview-functionaliteit wordt geleverd zonder overeenkomst op serviceniveau en wordt niet aanbevolen voor productieworkloads. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie. Er is momenteel geen portal of .NET SDK-ondersteuning.

De aangepaste **entiteit opzoeken** vaardigheid zoekt naar tekst uit een aangepaste, door de gebruiker gedefinieerde lijst van woorden en zinnen. Met behulp van deze lijst worden alle documenten labels met alle overeenkomende entiteiten. De vaardigheid ondersteunt ook een mate van fuzzy matching die kan worden toegepast om overeenkomsten te vinden die vergelijkbaar zijn, maar niet helemaal exact.  

Deze vaardigheid is niet gebonden aan een API voor Cognitive Services en kan gratis worden gebruikt tijdens de preview-periode. U moet echter nog steeds [een resource voor Cognitive Services koppelen](https://docs.microsoft.com/azure/search/cognitive-search-attach-cognitive-services)om de dagelijkse verrijkingslimiet te overschrijven. De dagelijkse limiet is van toepassing op gratis toegang tot cognitieve services wanneer deze wordt geopend via Azure Cognitive Search.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.CustomEntityLookupSkill 

## <a name="data-limits"></a>Gegevenslimieten
+ De maximale ondersteunde invoerrecordgrootte is 256 MB. Als u uw gegevens moet opsplitsen voordat u deze naar de aangepaste entiteitsopzoekvaardigheid verzendt, u overwegen de [vaardigheid Text Split te](cognitive-search-skill-textsplit.md)gebruiken.
+ De maximale definitietabel voor entiteiten die wordt ondersteund, is 10 MB als deze wordt geleverd met de parameter *TitiesDefinitionUri.* 
+ Als de entiteiten inline zijn gedefinieerd met de parameter *inlineEntitiesDefinition,* is de maximale ondersteunde grootte 10 KB.

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| entiteitenDefinitieUri    | Pad naar een JSON- of CSV-bestand met alle doeltekst waarop u wilt overeenkomen. Deze entiteitsdefinitie wordt gelezen aan het begin van een indexerrun; eventuele updates voor dit bestand halverwege de run zal niet worden gerealiseerd tot de volgende runs. Deze config moet toegankelijk zijn via HTTPS. Zie [Aangepaste entiteitsdefinitienotatie"](#custom-entity-definition-format) hieronder voor het verwachte CSV- of JSON-schema.|
|definitie van inlineEntiteiten | Inline JSON entiteitdefinities. Deze parameter vervangt de parameter titiesDefinitionUri indien aanwezig. Er mag niet meer dan 10 KB configuratie inline worden geleverd. Zie [Aangepaste entiteitsdefinitie](#custom-entity-definition-format) hieronder voor het verwachte JSON-schema. |
|standaardLanguageCode |    (Optioneel) Taalcode van de invoertekst die wordt gebruikt om invoertekst te tokeniseren en af te definiëren. De volgende talen worden `da, de, en, es, fi, fr, it, ko, pt`ondersteund: . De standaardinstelling`en`is Engels ( ). Als u een taalcode-landcode-indeling doorgeeft, wordt alleen het taalcodegedeelte van de indeling gebruikt.  |


## <a name="skill-inputs"></a>Vaardigheidsingangen

| Invoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| tekst          | De tekst om te analyseren.          |
| languageCode    | Optioneel. De standaardwaarde is `"en"`.  |


## <a name="skill-outputs"></a>Vaardigheidsuitvoer


| Uitvoernaam      | Beschrijving                   |
|---------------|-------------------------------|
| Entiteiten | Een array met objecten die informatie bevatten over de gevonden overeenkomsten en gerelateerde metagegevens. Elk van de geïdentificeerde entiteiten kan de volgende velden bevatten:  <ul> <li> *naam:* de entiteit op het hoogste niveau die is geïdentificeerd. De entiteit vertegenwoordigt het "genormaliseerde" formulier. </li> <li> *id:* een unieke id voor de entiteit zoals gedefinieerd door de gebruiker in de definitie-indeling 'Aangepaste entiteit'.</li> <li> *description*: Beschrijving van de entiteit zoals gedefinieerd door de gebruiker in de definitienotatie 'Aangepaste entiteit'. </li> <li> *type:* Entiteitstype zoals gedefinieerd door de gebruiker in de definitienotatie 'Aangepaste entiteit'.</li> <li> *subtype:* Subtype entiteit zoals gedefinieerd door de gebruiker in de definitienotatie 'Aangepaste entiteit'.</li>  <li> *overeenkomsten:* verzameling die elk van de overeenkomsten voor die entiteit op de brontekst beschrijft. Elke wedstrijd heeft de volgende leden: </li> <ul> <li> *tekst:* De ruwe tekst komt overeen met het brondocument. </li> <li> *verschuiving:* de locatie waar de overeenkomst in de tekst is gevonden. </li> <li> *lengte*: De lengte van de overeenkomende tekst. </li> <li> *matchDistance:* Het aantal tekens dat deze overeenkomst verschilt, was van de oorspronkelijke entiteitsnaam of alias.  </li> </ul> </ul>
  |

## <a name="custom-entity-definition-format"></a>Definitievan aangepaste entiteit

Er zijn 3 verschillende manieren om de lijst met aangepaste entiteiten aan de aangepaste entiteitopzoekvaardigheid te verstrekken. U de lijst in een. CSV-bestand, een . JSON-bestand of als inline-definitie als onderdeel van de vaardigheidsdefinitie.  

Als het definitiebestand een . CSV of . JSON-bestand, het pad van het bestand moet worden verstrekt als onderdeel van de parameter *EntiteitenDefinitionUri.* In dit geval wordt het bestand eenmaal gedownload aan het begin van elke indexerrun. Het bestand moet toegankelijk zijn zolang de indexer is bedoeld om uit te voeren. Ook moet het bestand worden gecodeerd UTF-8.

Als de definitie inline wordt verstrekt, moet deze worden opgegeven als inline als de inhoud van de vaardigheidsparameter *inlineEntiteitenDefinition.* 

### <a name="csv-format"></a>CSV-indeling

U de definitie opgeven van de aangepaste entiteiten die u moet zoeken in een CSV-bestand (Comma-Separated Value) door het pad naar het bestand op te geven en in te stellen in de vaardigheidsparameter *entiteitenDefinitionUri.* Het pad moet zich op een https-locatie bevinden. Het definitiebestand kan maximaal 10 MB groot zijn.

Het CSV-formaat is eenvoudig. Elke regel vertegenwoordigt een unieke entiteit, zoals hieronder wordt weergegeven:

```
Bill Gates, BillG, William H. Gates
Microsoft, MSFT
Satya Nadella 
```

In dit geval zijn er drie entiteiten die kunnen worden geretourneerd als entiteiten gevonden (Bill Gates, Satya Nadella, Microsoft), maar ze zullen worden geïdentificeerd als een van de termen op de regel (aliassen) worden afgestemd op de tekst. Als de tekenreeks 'William H. Gates' bijvoorbeeld in een document wordt gevonden, wordt een overeenkomst voor de entiteit "Bill Gates" geretourneerd.

### <a name="json-format"></a>JSON-indeling

U de definitie van de aangepaste entiteiten om te zoeken in een JSON-bestand ook. De JSON-indeling geeft u een beetje meer flexibiliteit, omdat u hiermee overeenkomende regels per term definiëren. U bijvoorbeeld de fuzzy matching distance (Damerau-Levenshtein afstand) voor elke term opgeven of dat de matching casegevoelig moet zijn of niet. 

 Net als bij CSV-bestanden moet u het pad naar het JSON-bestand opgeven en instellen in de vaardigheidsparameter *entiteitenDefinitionUri.* Het pad moet zich op een https-locatie bevinden. Het definitiebestand kan maximaal 10 MB groot zijn.

De meest elementaire lijstmeting van de aangepaste JSON-entiteit kan een lijst zijn met entiteiten die overeenkomen:

```json
[ 
    { 
        "name" : "Bill Gates"
    }, 
    { 
        "name" : "Microsoft"
    }, 
    { 
        "name" : "Satya Nadella"
    }
]
```

Een complexer voorbeeld van een JSON-definitie kan optioneel het id-id-, beschrijvings-, type- en subtype van elke entiteit bieden , evenals andere *aliassen*. Als een aliasterm is gekoppeld, wordt de entiteit ook geretourneerd:

```json
[ 
    { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
    }, 
    { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
    }, 
    { 
        "name" : "LinkedIn" , 
        "description" : "The LinkedIn company", 
        "id" : "differentIdentifyingScheme123", 
        "fuzzyEditDistance" : 0 
    }, 
    { 
        "name" : "Microsoft" , 
        "description" : "Microsoft Corporation", 
        "id" : "differentIdentifyingScheme987", 
        "defaultCaseSensitive" : false, 
        "defaultFuzzyEditDistance" : 1, 
        "aliases" : [ 
            { "text" : "MSFT", "caseSensitive" : true }
        ]
    } 
] 
```

De onderstaande tabellen beschrijven in meer details de verschillende configuratieparameters die u instellen bij het definiëren van de entiteiten die overeenkomen:

|  Veldnaam  |        Beschrijving  |
|--------------|----------------------|
| name | De entiteitsscriptor op het hoogste niveau. Overeenkomsten in de vaardigheidsuitvoer worden gegroepeerd op deze naam en moet de "genormaliseerde" vorm van de gevonden tekst vertegenwoordigen.  |
| description  | (Optioneel) Dit veld kan worden gebruikt als een doorgeefinformatie voor aangepaste metagegevens over de overeenkomende tekst(en). De waarde van dit veld wordt weergegeven bij elke overeenkomst van zijn entiteit in de vaardigheidsuitvoer. |
| type | (Optioneel) Dit veld kan worden gebruikt als een doorgeefinformatie voor aangepaste metagegevens over de overeenkomende tekst(en). De waarde van dit veld wordt weergegeven bij elke overeenkomst van zijn entiteit in de vaardigheidsuitvoer. |
| Subtype | (Optioneel) Dit veld kan worden gebruikt als een doorgeefinformatie voor aangepaste metagegevens over de overeenkomende tekst(en). De waarde van dit veld wordt weergegeven bij elke overeenkomst van zijn entiteit in de vaardigheidsuitvoer. |
| id | (Optioneel) Dit veld kan worden gebruikt als een doorgeefinformatie voor aangepaste metagegevens over de overeenkomende tekst(en). De waarde van dit veld wordt weergegeven bij elke overeenkomst van zijn entiteit in de vaardigheidsuitvoer. |
| hoofdlettergevoelig | (Optioneel) Standaard onjuist. Booleaanse waarde die aangeeft of vergelijkingen met de entiteitsnaam gevoelig moeten zijn voor tekenbehuizing. Voorbeeld geval ongevoelige wedstrijden van "Microsoft" zou kunnen zijn: Microsoft, microSoft, MICROSOFT |
| fuzzyEditDistance | (Optioneel) Standaard op 0. Maximale waarde van 5. Geeft het aanvaardbare aantal verschillende tekens aan dat nog steeds overeenkomt met de naam van de entiteit. De kleinst mogelijke vaagheid voor een bepaalde wedstrijd wordt geretourneerd.  Als de bewerkingsafstand bijvoorbeeld is ingesteld op 3, komt 'Windows 10' nog steeds overeen met 'Windows', 'Windows10' en 'windows 7'. <br/> Wanneer de gevoeligheid van het geval is ingesteld op false, tellen gevalverschillen NIET mee voor vaagheidstolerantie, maar anders wel. |
| defaultCaseSensitive defaultCaseSensitive | (Optioneel) Hiermee wijzigt u de standaardgevoeligheidswaarde voor deze entiteit. Het wordt gebruikt om de standaardwaarde van alle aliassen caseSensitive waarden te wijzigen. |
| standaardFuzzyEditDistance | (Optioneel) Hiermee wijzigt u de standaardwaarde voor de afstand voor deze entiteit. Het kan worden gebruikt om de standaardwaarde van alle aliassen fuzzyEditDistance waarden te wijzigen. |
| Aliassen | (Optioneel) Een array met complexe objecten die kunnen worden gebruikt om alternatieve spellingen of synoniemen op te geven aan de naam van de hoofdentiteit. |

| Aliaseigenschappen | Beschrijving |
|------------------|-------------|
| tekst  | De alternatieve spelling of representatie van een doelentiteitnaam.  |
| hoofdlettergevoelig | (Optioneel) Werkt hetzelfde als root entiteit "caseSensitive" parameter hierboven, maar is van toepassing op alleen deze ene alias. |
| fuzzyEditDistance | (Optioneel) Werkt hetzelfde als de parameter "fuzzyEditDistance" hierboven, maar is alleen van toepassing op deze ene alias. |


### <a name="inline-format"></a>Inline-indeling

In sommige gevallen is het handiger om de lijst met aangepaste entiteiten op te geven die inline rechtstreeks in de vaardigheidsdefinitie kunnen koppelen. In dat geval u een vergelijkbare JSON-indeling gebruiken als de hierboven beschreven indeling, maar het is ingevoerd in de vaardigheidsdefinitie.
Alleen configuraties met een grootte van minder dan 10 KB (geserialiseerde grootte) kunnen inline worden gedefinieerd. 

##    <a name="sample-definition"></a>Voorbeelddefinitie

Hieronder wordt een voorbeeldvaardigheidsdefinitie weergegeven met behulp van een inline-indeling:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "inlineEntitiesDefinition": 
    [
      { 
        "name" : "Bill Gates",
        "description" : "Microsoft founder." ,
        "aliases" : [ 
            { "text" : "William H. Gates", "caseSensitive" : false },
            { "text" : "BillG", "caseSensitive" : true }
        ]
      }, 
      { 
        "name" : "Xbox One", 
        "type": "Harware",
        "subtype" : "Gaming Device",
        "id" : "4e36bf9d-5550-4396-8647-8e43d7564a76",
        "description" : "The Xbox One product"
      }
    ],    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }
```
Als u besluit een aanwijzer te geven aan het definitiebestand van entiteiten, wordt hieronder een voorbeeldvaardigheidsdefinitie weergegeven met de indeling entiteitenDefinitionUri:

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.CustomEntityLookupSkill",
    "context": "/document",
    "entitiesDefinitionUri": "https://myblobhost.net/keyWordsConfig.csv",    
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "entities",
        "targetName": "matchedEntities"
      }
    ]
  }

```

##    <a name="sample-input"></a>Voorbeeldinvoer

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "The company microsoft was founded by Bill Gates. Microsoft's gaming console is called Xbox",
             "languageCode": "en"
           }
      }
    ]
}
```

##    <a name="sample-output"></a>Voorbeelduitvoer

```json
  { 
    "values" : 
    [ 
      { 
        "recordId": "1", 
        "data" : { 
          "entities": [
            { 
              "name" : "Microsoft", 
              "description" : "This document refers to Microsoft the company", 
              "id" : "differentIdentifyingScheme987", 
              "matches" : [ 
                { 
                  "text" : "microsoft", 
                  "offset" : 13, 
                  "length" : 9, 
                  "matchDistance" : 0 
                }, 
                { 
                  "text" : "Microsoft",
                  "offset" : 49, 
                  "length" : 9, 
                  "matchDistance" : 0
                }
              ] 
            },
            { 
              "name" : "Bill Gates",
              "description" : "William Henry Gates III, founder of Microsoft.", 
              "matches" : [
                { 
                  "text" : "Bill Gates",
                  "offset" : 37, 
                  "length" : 10,
                  "matchDistance" : 0 
                }
              ]
            }
          ] 
        } 
      } 
    ] 
  } 
```

## <a name="errors-and-warnings"></a>Fouten en waarschuwingen

### <a name="warning-reached-maximum-capacity-for-matches-skipping-all-further-duplicate-matches"></a>Waarschuwing: Bereikt maximale capaciteit voor wedstrijden, het overslaan van alle verdere dubbele wedstrijden.

Deze waarschuwing wordt weergegeven als het aantal gedetecteerde overeenkomsten groter is dan het maximaal toegestane aantal. In dit geval stoppen we met het opnemen van dubbele overeenkomsten. Als dit voor u onaanvaardbaar is, dient u een [ondersteuningsticket](https://ms.portal.azure.com/#create/Microsoft.Support) in, zodat we u kunnen helpen met uw individuele use case.

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Entiteitherkenningsvaardigheid (om te zoeken naar bekende entiteiten)](cognitive-search-skill-entity-recognition.md)
