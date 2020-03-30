---
title: Cognitieve vaardigheid van shaper
titleSuffix: Azure Cognitive Search
description: Metagegevens en gestructureerde informatie extraheren uit ongestructureerde gegevens en vorm deze als een complex type in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 384b79037bb30656934c5e4b596dac2b776593b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754113"
---
# <a name="shaper-cognitive-skill"></a>Cognitieve vaardigheid van shaper

De **Shaper-vaardigheid** consolideert verschillende ingangen in een [complex type](search-howto-complex-data-types.md) waarnaar later in de verrijkingspijplijn kan worden verwezen. Met **de Shaper-vaardigheid** u in wezen een structuur maken, de naam van de leden van die structuur definiëren en waarden toewijzen aan elk lid. Voorbeelden van geconsolideerde velden die nuttig zijn in zoekscenario's zijn het combineren van een voor- en achternaam in één structuur, stad en staat in één structuur, of naam en geboortedatum in één structuur om een unieke identiteit tot stand te brengen.

Bovendien voegt de **shaper-vaardigheid** die in [scenario 3](#nested-complex-types) wordt geïllustreerd, een optionele *eigenschap sourceContext* toe aan de invoer. De *eigenschappen bron* en *bronContext* sluiten elkaar uit. Als de invoer zich op de context van de vaardigheid bevindt, gebruikt u gewoon *bron*. Als de invoer zich in een *andere* context bevindt dan de vaardigheidscontext, gebruikt u de *bronContext*. De *bronContext* vereist dat u een geneste invoer definieert met het specifieke element dat als bron wordt geadresseerd. 

De uitvoernaam is altijd "output". Intern kan de pijplijn een andere naam in kaart brengen, zoals 'analyzedText' zoals weergegeven in de onderstaande voorbeelden, maar de **Shaper-vaardigheid** zelf retourneert 'uitvoer' in het antwoord. Dit kan belangrijk zijn als u verrijkte documenten debuggen en de naamgevingsdiscrepantie opmerkt, of als u een aangepaste vaardigheid opbouwt en de respons zelf structureert.

> [!NOTE]
> De **Shaper-vaardigheid** is niet gebonden aan een API voor Cognitive Services en u wordt niet in rekening gebracht voor het gebruik ervan. U moet echter nog steeds [een resource voor Cognitive Services koppelen](cognitive-search-attach-cognitive-services.md)om de optie **Gratis** resource te overschrijven die u beperkt tot een klein aantal dagelijkse verrijkingen per dag.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Scenario 1: complexe typen

Overweeg een scenario waarin u een structuur wilt maken die *analyzedText* wordt genoemd en die twee leden heeft: *respectievelijk tekst* en *sentiment.* In een index wordt een doorzoekbaar veld met meerdere delen een *complex type* genoemd en wordt het vaak gemaakt wanneer brongegevens een overeenkomstige complexe structuur hebben die erop wordt toegewezen.

Een andere benadering voor het maken van complexe typen is echter de **Shaper-vaardigheid.** Door deze vaardigheid op te tellen in een skillset, kunnen de in-memory bewerkingen tijdens skillset processing gegevensvormen uitvoeren met geneste structuren, die vervolgens kunnen worden toegewezen aan een complex type in uw index. 

In de volgende voorbeeldvaardigheidsdefinitie worden de ledennamen als invoer gegeven. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Voorbeeldindex

Een skillset wordt aangeroepen door een indexeerder en een indexer vereist een index. Een complexe veldweergave in uw index lijkt mogelijk op het volgende voorbeeld. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Vaardigheidsinvoer

Een binnenkomend JSON-document met bruikbare invoer voor deze **Shaper-vaardigheid** kan zijn:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Vaardigheidsuitvoer

De **Shaper-vaardigheid** genereert een nieuw element genaamd *analyzedText* met de gecombineerde elementen van *tekst* en *sentiment.* Deze uitvoer voldoet aan het indexschema. Het wordt geïmporteerd en geïndexeerd in een Azure Cognitive Search-index.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Scenario 2: invoerconsolidatie

Stel je in een ander voorbeeld voor dat je in verschillende stadia van pijplijnverwerking de titel van een boek en hoofdstuktitels op verschillende pagina's van het boek hebt uitgepakt. U nu één structuur maken die bestaat uit deze verschillende ingangen.

De **shaper-vaardigheidsdefinitie** voor dit scenario lijkt mogelijk op het volgende voorbeeld:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Vaardigheidsuitvoer
In dit geval vlakt de **Shaper** alle hoofdstuktitels af om één array te maken. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Scenario 3: invoerconsolidatie van geneste contexten

Stel je hebt de titel, hoofdstukken en de inhoud van een boek en hebben uitgevoerd entiteit erkenning en sleutelzinnen op de inhoud en nu nodig hebt om de resultaten van de verschillende vaardigheden te splitsen in een enkele vorm met het hoofdstuk naam, entiteiten, en de belangrijkste zinnen.

De **shaper-vaardigheidsdefinitie** voor dit scenario lijkt mogelijk op het volgende voorbeeld:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Vaardigheidsuitvoer
In dit geval maakt de **Shaper** een complex type. Deze structuur bestaat in het geheugen. Als u het wilt opslaan in een [kenniswinkel,](knowledge-store-concept-intro.md)moet u een projectie maken in uw vaardighedendie opslagkenmerken definieert.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Zie ook

+ [Ingebouwde vaardigheden](cognitive-search-predefined-skills.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Complexe typen gebruiken](search-howto-complex-data-types.md)
+ [Knowledge Store (preview)](knowledge-store-concept-intro.md)
+ [Maak een kenniswinkel in REST](knowledge-store-create-rest.md)