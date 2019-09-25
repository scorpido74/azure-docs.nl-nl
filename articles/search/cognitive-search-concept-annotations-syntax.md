---
title: Referentie-invoer en-uitvoer in cognitieve Zoek pijplijnen-Azure Search
description: Hierin worden de syntaxis van aantekeningen beschreven en wordt uitgelegd hoe u kunt verwijzen naar een aantekening in de invoer en uitvoer van een vakkennisset in een cognitieve Zoek pijplijn in Azure Search.
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: 40559744f0650c64afb1dc63c38f56efaa0219d7
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265540"
---
# <a name="how-to-reference-annotations-in-a-cognitive-search-skillset"></a>Verwijzen naar aantekeningen in een cognitieve Zoek vaardighedenset

In dit artikel leert u hoe u kunt verwijzen naar aantekeningen in vaardigheids definities, met voor beelden om verschillende scenario's te illustreren. Als de inhoud van een document door een set met vaardig heden loopt, wordt het verrijkt met annotaties. Annotaties kunnen worden gebruikt als invoer voor een verdere verrijking van de downstream of worden toegewezen aan een uitvoer veld in een index. 
 
Voor beelden in dit artikel zijn gebaseerd op het *inhouds* veld dat automatisch door [Azure Blob-Indexeer functies](search-howto-indexing-azure-blob-storage.md) wordt gegenereerd als onderdeel van de fase voor het kraken van het document. Wanneer u verwijst naar documenten uit een BLOB-container, gebruikt u een `"/document/content"`indeling zoals, waarbij het veld *inhoud* deel uitmaakt van het *document*. 

## <a name="background-concepts"></a>Achtergrond concepten

Voordat u de syntaxis bekijkt, gaan we enkele belang rijke concepten opnieuw bezoeken om meer inzicht te krijgen in de voor beelden die verderop in dit artikel worden beschreven.

| Begrip | Description |
|------|-------------|
| Verrijkt document | Een verrijkt document is een interne structuur die door de pijp lijn wordt gemaakt en gebruikt om alle aantekeningen te bewaren die betrekking hebben op een document. U beschouwt een verrijkt document als een structuur van aantekeningen. Over het algemeen wordt een aantekening die is gemaakt van een vorige aantekening het onderliggende item.<p/>Verrijkte documenten bestaan alleen voor de duur van de uitvoering van de vaardig heden. Zodra de inhoud is toegewezen aan de zoek index, is het verrijkte document niet langer nodig. Hoewel u niet rechtstreeks met verrijkde documenten communiceert, is het handig om een geestelijke model van de documenten te hebben wanneer u een vaardig heden maakt. |
| Verrijkings context | De context waarin de verrijking plaatsvindt, in termen van welk element wordt verrijkt. De verrijkings context bevindt zich standaard `"/document"` op het niveau, in het bereik van afzonderlijke documenten. Wanneer een vaardigheid wordt uitgevoerd, worden de uitvoer van die vaardigheid [Eigenschappen van de gedefinieerde context](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Voorbeeld 1: Eenvoudige aantekening verwijzing

In Azure Blob-opslag hebt u een verscheidenheid aan bestanden met verwijzingen naar namen van personen die u wilt extra heren met behulp van entiteits herkenning. In de onderstaande `"/document/content"` vaardigheids definitie is de tekstuele weer gave van het hele document en ' personen ' is een uittreksel van volledige namen voor entiteiten die als personen zijn geïdentificeerd.

Omdat de standaard context is `"/document"`, kan er nu naar de lijst met personen worden verwezen `"/document/people"`als. In dit specifieke geval `"/document/people"` is een aantekening die nu kan worden toegewezen aan een veld in een index of worden gebruikt in een andere vaardigheid in dezelfde vaardig heden.

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>Voorbeeld 2: Verwijzen naar een matrix in een document

In dit voor beeld wordt een eerdere versie van het document beschreven, waarin wordt uitgelegd hoe u een verrijkings stap meerdere keren aanroept. Stel dat het vorige voor beeld een matrix met teken reeksen heeft gegenereerd met 10 personen namen uit één document. Een redelijke volgende stap kan een tweede verrijking zijn die de achternaam ophaalt uit een volledige naam. Omdat er tien namen zijn, wilt u deze stap 10 keer in dit document aanroepen, één keer voor elke persoon. 

Als u het juiste aantal iteraties wilt aanroepen, stelt u `"/document/people/*"`de context in op als`"*"`, waarbij het sterretje () alle knoop punten in het verrijkte `"/document/people"`document vertegenwoordigt als descendanten van. Hoewel deze vaardigheid slechts eenmaal in de vaardigheden matrix is gedefinieerd, wordt deze voor elk lid binnen het document aangeroepen totdat alle leden zijn verwerkt.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

Wanneer aantekeningen matrices of verzamelingen teken reeksen zijn, wilt u mogelijk specifieke leden richten in plaats van de matrix als geheel. In het bovenstaande voor beeld wordt een aantekening gegenereerd die wordt aangeroepen `"last"` onder elk knoop punt dat wordt weer gegeven door de context. Als u naar deze reeks aantekeningen wilt verwijzen, kunt u de syntaxis `"/document/people/*/last"`gebruiken. Als u wilt verwijzen naar een bepaalde aantekening, kunt u een expliciete index gebruiken `"/document/people/1/last`: "om te verwijzen naar de achternaam van de eerste persoon die in het document is geïdentificeerd. U ziet dat in deze syntaxis matrices de waarde 0 Indexed is.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Voorbeeld 3: Verwijzen naar leden binnen een matrix

Soms moet u alle aantekeningen van een bepaald type groeperen om deze door te geven aan een bepaalde vaardigheid. Overweeg een hypothetische aangepaste vaardigheid die de meest voorkomende achternaam identificeert van de achternamen die in voor beeld 2 zijn geëxtraheerd. Als u alleen de laatste namen wilt opgeven voor de aangepaste vaardigheid, geeft u `"/document"` de context op als `"/document/people/*/lastname"`en de invoer als.

U ziet dat de kardinaliteit `"/document/people/*/lastname"` van groter is dan die van het document. Er kunnen 10 achternaam-knoop punten zijn zolang er slechts één document knooppunt is voor dit document. In dat geval wordt met het systeem automatisch een matrix gemaakt met `"/document/people/*/lastname"` alle elementen in het document.

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>Zie ook
+ [Een aangepaste vaardigheid integreren in een verrijkings pijplijn](cognitive-search-custom-skill-interface.md)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Vaardig heden maken (REST)](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Uitgebreide velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
