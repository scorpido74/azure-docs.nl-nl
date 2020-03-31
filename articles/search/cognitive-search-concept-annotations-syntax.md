---
title: Referentie-invoer en -uitvoer in skillsets
titleSuffix: Azure Cognitive Search
description: Hiermee wordt de annotatiesyntaxis uitgelegd en hoe u verwijst naar een annotatie in de ingangen en uitvoer van een skillset in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e27f61239c0631fb248217777a311b13ee48a3f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113869"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Hoe verwijst u naar annotaties in een Azure Cognitive Search-skillset

In dit artikel leert u hoe u annotaties in vaardigheidsdefinities verwijzen, met behulp van voorbeelden om verschillende scenario's te illustreren. Als de inhoud van een document door een reeks vaardigheden stroomt, wordt het verrijkt met annotaties. Annotaties kunnen worden gebruikt als ingangen voor verdere downstream verrijking, of toegewezen aan een uitvoerveld in een index. 
 
Voorbeelden in dit artikel zijn gebaseerd op het *inhoudsveld* dat automatisch wordt gegenereerd door [Azure Blob-indexeerders](search-howto-indexing-azure-blob-storage.md) als onderdeel van de fase van het kraken van documenten. Wanneer u verwijst naar documenten uit een Blob-container, gebruikt u een indeling zoals `"/document/content"`, waarbij het *inhoudsveld* deel uitmaakt van het *document*. 

## <a name="background-concepts"></a>Achtergrondconcepten

Voordat we de syntaxis bekijken, bekijken we een aantal belangrijke concepten om de voorbeelden die later in dit artikel worden gegeven, beter te begrijpen.

| Termijn | Beschrijving |
|------|-------------|
| Verrijkt document | Een verrijkt document is een interne structuur die door de pijplijn is gemaakt en gebruikt om alle annotaties met betrekking tot een document te bevatten. Denk aan een verrijkt document als een boom van annotaties. Over het algemeen wordt een annotatie die is gemaakt van een eerdere annotatie zijn kind.<p/>Verrijkte documenten bestaan alleen voor de duur van de uitvoering van vaardigheden. Zodra de inhoud is toegewezen aan de zoekindex, is het verrijkte document niet meer nodig. Hoewel u niet rechtstreeks met verrijkte documenten communiceert, is het handig om een mentaal model van de documenten te hebben bij het maken van een vaardighedenset. |
| Verrijkingscontext | De context waarin de verrijking plaatsvindt, in termen van welk element is verrijkt. Standaard bevindt de verrijkingscontext zich op het `"/document"` niveau dat is ingesteld op afzonderlijke documenten. Wanneer een vaardigheid wordt uitgevoerd, worden de uitvoer van die vaardigheid [eigenschappen van de gedefinieerde context](#example-2).|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>Voorbeeld 1: Eenvoudige annotatieverwijzing

Stel dat u in Azure Blob-opslag verschillende bestanden hebt die verwijzingen bevatten naar de namen van mensen die u wilt extraheren met behulp van entiteitsherkenning. In de onderstaande `"/document/content"` vaardigheidsdefinitie is de tekstuele weergave van het hele document, en "mensen" is een extractie van volledige namen voor entiteiten die als personen zijn geïdentificeerd.

Omdat de standaardcontext is, `"/document"`kan de lijst `"/document/people"`met personen nu worden aangeduid als . In dit `"/document/people"` specifieke geval is een annotatie, die nu kan worden toegewezen aan een veld in een index, of gebruikt in een andere vaardigheid in dezelfde vaardigheden.

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

## <a name="example-2-reference-an-array-within-a-document"></a>Voorbeeld 2: Een array in een document verwijzen

In dit voorbeeld wordt voortgebouwd op het vorige voorbeeld en wordt u weergegeven hoe u een verrijkingsstap meerdere keren boven hetzelfde document aanroepen. Stel dat het vorige voorbeeld een reeks tekenreeksen heeft gegenereerd met 10 personennamen uit één document. Een redelijke volgende stap kan een tweede verrijking zijn die de achternaam uit een volledige naam haalt. Omdat er 10 namen zijn, wilt u dat deze stap 10 keer wordt aangeroepen in dit document, één keer voor elke persoon. 

Als u het juiste aantal iteraties `"/document/people/*"`wilt aanroepen, stelt`"*"`u de context in als , `"/document/people"`waarbij het sterretje ( ) alle knooppunten in het verrijkte document vertegenwoordigt als afstammelingen van . Hoewel deze vaardigheid slechts eenmaal wordt gedefinieerd in de array vaardigheden, wordt het voor elk lid binnen het document gevraagd totdat alle leden zijn verwerkt.

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

Wanneer annotaties arrays of verzamelingen tekenreeksen zijn, u specifieke leden targeten in plaats van op de array als geheel. Het bovenstaande voorbeeld genereert een `"last"` annotatie die onder elk knooppunt wordt aangeroepen dat door de context wordt weergegeven. Als u wilt verwijzen naar deze familie van annotaties, u de syntaxis `"/document/people/*/last"`gebruiken. Als u naar een bepaalde annotatie wilt verwijzen, `"/document/people/1/last`kunt u een expliciete index gebruiken: " om naar de achternaam van de eerste persoon in het document te verwijzen. Merk op dat in deze syntaxisarrays "0 geïndexeerd" zijn.

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>Voorbeeld 3: Referentieleden binnen een array

Soms moet je alle annotaties van een bepaald type groeperen om ze door te geven aan een bepaalde vaardigheid. Overweeg een hypothetische aangepaste vaardigheid die de meest voorkomende achternaam identificeert van alle achternamen die zijn geëxtraheerd in voorbeeld 2. Als u alleen de achternamen wilt opgeven `"/document"` aan de `"/document/people/*/lastname"`aangepaste vaardigheid, geeft u de context als en de invoer op als .

Merk op dat `"/document/people/*/lastname"` de kardinaliteit van groter is dan die van document. Er kunnen 10 lastnameknooppunten zijn, terwijl er slechts één documentknooppunt voor dit document is. In dat geval maakt het systeem automatisch `"/document/people/*/lastname"` een array met alle elementen in het document.

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
+ [Een aangepaste vaardigheid integreren in een verrijkingspijplijn](cognitive-search-custom-skill-interface.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Skillset (REST) maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Verrijkte velden toewijzen aan een index](cognitive-search-output-field-mapping.md)
