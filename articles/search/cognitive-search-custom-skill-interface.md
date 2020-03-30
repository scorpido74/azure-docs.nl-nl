---
title: Interfacedefinitie voor aangepaste vaardigheden
titleSuffix: Azure Cognitive Search
description: Aangepaste gegevensextractie-interface voor aangepaste web-api-vaardigheid in een AI-verrijkingspijplijn in Azure Cognitive Search.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 78f5f6eda28bed164668445b5671dad92f8dedd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500256"
---
# <a name="how-to-add-a-custom-skill-to-an-azure-cognitive-search-enrichment-pipeline"></a>Een aangepaste vaardigheid toevoegen aan een Azure Cognitive Search-verrijkingspijplijn

Een [verrijkingspijplijn](cognitive-search-concept-intro.md) in Azure Cognitive Search kan worden samengesteld op basis van [ingebouwde cognitieve vaardigheden](cognitive-search-predefined-skills.md) en aangepaste [vaardigheden](cognitive-search-custom-skill-web-api.md) die u persoonlijk maakt en aan de pijplijn toevoegt. In dit artikel leert u hoe u een aangepaste vaardigheid maakt die een interface blootlegt waarmee deze kan worden opgenomen in een AI-verrijkingspijplijn. 

Het bouwen van een aangepaste vaardigheid geeft u een manier om transformaties in te voegen die uniek zijn voor uw inhoud. Een aangepaste vaardigheid wordt onafhankelijk uitgevoerd en past elke verrijkingsstap toe die u nodig hebt. U bijvoorbeeld veldspecifieke aangepaste entiteiten definiëren, aangepaste classificatiemodellen maken om zakelijke en financiële contracten en documenten te differentiëren of een spraakherkenningsvaardigheid toevoegen om dieper in audiobestanden voor relevante inhoud te komen. Zie [Voorbeeld: Een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)voor een stapsgewijs voorbeeld.

 Welke aangepaste mogelijkheden u ook nodig hebt, er is een eenvoudige en duidelijke interface voor het aansluiten van een aangepaste vaardigheid op de rest van de verrijkingspijplijn. De enige vereiste voor opname in een [skillset](cognitive-search-defining-skillset.md) is de mogelijkheid om inputs te accepteren en uitgangen uit te zenden op manieren die binnen de vaardigheden als geheel worden verbruikt. De focus van dit artikel ligt op de invoer- en uitvoernotaties die de verrijkingspijplijn vereist.

## <a name="web-api-custom-skill-interface"></a>Aangepaste vaardigheidsinterface voor web-API

Aangepaste WebAPI-vaardigheidseindpunten standaard een time-out als ze geen antwoord binnen een venster van 30 seconden retourneren. De indexeringspijplijn is synchroon en indexering levert een time-outfout op als een antwoord niet in dat venster wordt ontvangen.  Het is mogelijk om de time-out te configureren op maximaal 230 seconden, door de parameter time-out in te stellen:

```json
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill has a 230 second timeout",
        "uri": "https://[your custom skill uri goes here]",
        "timeout": "PT230S",
```

Controleer of de URI veilig is (HTTPS).

Momenteel is het enige mechanisme voor interactie met een aangepaste vaardigheid via een Web API-interface. De web-API-behoeften moeten voldoen aan de vereisten die in deze sectie worden beschreven.

### <a name="1--web-api-input-format"></a>1. Web API-invoerindeling

De Web API moet een array met records accepteren die moeten worden verwerkt. Elke record moet een "eigenschappenzak" bevatten die de invoer is die wordt geleverd aan uw web-API. 

Stel dat u een eenvoudige verrijking wilt maken die de eerste datum identificeert die in de tekst van een contract wordt vermeld. In dit voorbeeld accepteert de vaardigheid één *invoercontractTekst* als contracttekst. De vaardigheid heeft ook een enkele output, dat is de datum van het contract. Om de verrijking interessanter te maken, retourneert u deze *contractDate* in de vorm van een meerdelige complexe type.

Uw web-API moet klaar zijn om een batch invoerrecords te ontvangen. Elk lid van de *array waarden* vertegenwoordigt de invoer voor een bepaalde record. Elke record is vereist om de volgende elementen te hebben:

+ Een *recordId-lid* dat de unieke id is voor een bepaalde record. Wanneer uw verrijkende de resultaten retourneert, moet deze *recordId* worden opgegeven om de beller in staat te stellen de recordresultaten aan zijn invoer te koppelen.

+ Een *gegevenslid,* dat in wezen een zak invoervelden is voor elke record.

Om concreter te zijn, moet uw web-API volgens het bovenstaande voorbeeld aanvragen verwachten die er als volgt uitzien:

```json
{
    "values": [
      {
        "recordId": "a1",
        "data":
           {
             "contractText": 
                "This is a contract that was issues on November 3, 2017 and that involves... "
           }
      },
      {
        "recordId": "b5",
        "data":
           {
             "contractText": 
                "In the City of Seattle, WA on February 5, 2018 there was a decision made..."
           }
      },
      {
        "recordId": "c3",
        "data":
           {
             "contractText": null
           }
      }
    ]
}
```
In werkelijkheid kan uw dienst worden gebeld met honderden of duizenden records in plaats van alleen de drie hier getoond.

### <a name="2-web-api-output-format"></a>2. Web API-uitvoerindeling

Het formaat van de uitvoer is een set records met een *recordId*en een eigendomszak 

```json
{
  "values": 
  [
      {
        "recordId": "b5",
        "data" : 
        {
            "contractDate":  { "day" : 5, "month": 2, "year" : 2018 }
        }
      },
      {
        "recordId": "a1",
        "data" : {
            "contractDate": { "day" : 3, "month": 11, "year" : 2017 }                    
        }
      },
      {
        "recordId": "c3",
        "data" : 
        {
        },
        "errors": [ { "message": "contractText field required "}   ],  
        "warnings": [ {"message": "Date not found" }  ]
      }
    ]
}
```

In dit specifieke voorbeeld is slechts één uitvoer uitgevoerd, maar u meer dan één eigenschap produceren. 

### <a name="errors-and-warning"></a>Fouten en waarschuwing

Zoals in het vorige voorbeeld wordt weergegeven, u voor elke record fout- en waarschuwingsberichten retourneren.

## <a name="consuming-custom-skills-from-skillset"></a>Het consumeren van aangepaste vaardigheden van vaardigheden

Wanneer u een web-API-verrijking maakt, u HTTP-headers en -parameters beschrijven als onderdeel van de aanvraag. In het onderstaande fragment ziet u hoe aanvraagparameters en *optionele* HTTP-headers kunnen worden beschreven als onderdeel van de definitie van vaardigheden. HTTP-headers zijn geen vereiste, maar ze stellen u in staat om extra configuratiemogelijkheden toe te voegen aan uw vaardigheden en deze in te stellen vanuit de definitie van vaardigheden.

```json
{
    "skills": [
      {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "This skill calls an Azure function, which in turn calls TA sentiment",
        "uri": "https://indexer-e2e-webskill.azurewebsites.net/api/DateExtractor?language=en",
        "context": "/document",
        "httpHeaders": {
            "DateExtractor-Api-Key": "foo"
        },
        "inputs": [
          {
            "name": "contractText",
            "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "contractDate",
            "targetName": "date"
          }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Volgende stappen

Dit artikel ging over de interfacevereisten die nodig zijn voor het integreren van een aangepaste vaardigheid in een skillset. Klik op de volgende links voor meer informatie over aangepaste vaardigheden en vaardigheden.

+ [Power Skills: een opslagplaats van aangepaste vaardigheden](https://github.com/Azure-Samples/azure-search-power-skills)
+ [Voorbeeld: Een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)
+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Skillset (REST) maken](https://docs.microsoft.com/rest/api/searchservice/create-skillset)
+ [Verrijkte velden in kaart brengen](cognitive-search-output-field-mapping.md)
