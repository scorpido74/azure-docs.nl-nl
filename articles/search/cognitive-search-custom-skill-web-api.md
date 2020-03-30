---
title: Aangepaste Web API-vaardigheid in skillsets
titleSuffix: Azure Cognitive Search
description: Breid de mogelijkheden van Azure Cognitive Search-skillsets uit door naar web-API's te roepen. Gebruik de custom web API-vaardigheid om uw aangepaste code te integreren.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 29928d78c2cfc2f21def363341f8383c4efa89d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74484110"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Aangepaste web-API-vaardigheid in een Azure Cognitive Search-verrijkingspijplijn

Met de **custom web API-vaardigheid** u AI-verrijking uitbreiden door aan te roepen naar een Web API-eindpunt dat aangepaste bewerkingen biedt. Net als ingebouwde vaardigheden heeft een **Custom Web API-vaardigheid** ingangen en uitgangen. Afhankelijk van de ingangen ontvangt uw Web API een JSON-payload wanneer de indexer wordt uitgevoerd en voert een JSON-payload uit als reactie, samen met een statuscode voor succes. Het antwoord wordt verwacht dat de uitgangen opgegeven door uw aangepaste vaardigheid. Elke andere reactie wordt beschouwd als een fout en er worden geen verrijkingen uitgevoerd.

De structuur van de JSON payloads worden verderop in dit document beschreven.

> [!NOTE]
> De indexer zal twee maal opnieuw proberen voor bepaalde standaard HTTP-statuscodes die zijn geretourneerd uit de web-API. Deze HTTP-statuscodes zijn: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Vaardigheidsparameters

Parameters zijn hoofdlettergevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| Uri | De URI van de Web API waarnaar de _JSON_ payload wordt verzonden. Alleen **https** URI-regeling is toegestaan |
| httpMethod httpMethod | De methode om te gebruiken tijdens het verzenden van de payload. Toegestane methoden zijn `PUT` of`POST` |
| httpHeaders | Een verzameling sleutelwaardeparen waarbij de sleutels kopnamen en waarden vertegenwoordigen, vertegenwoordigen kopwaarden die samen met de payload naar uw web-API worden verzonden. Het is verboden de volgende kopteksten `Accept-Charset` `Accept-Encoding`in `Content-Length` `Content-Type`deze `Cookie` `Host`verzameling `TE` `Upgrade`te gebruiken: `Accept`, , , , ,`Via` |
| timeout | (Optioneel) Wanneer opgegeven, geeft u de time-out voor de http-client die de API-aanroep maakt. Het moet worden opgemaakt als een XSD "dayTimeDuration" waarde (een beperkte subset van een [ISO 8601 duurwaarde).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Bijvoorbeeld, `PT60S` voor 60 seconden. Als dit niet is ingesteld, wordt een standaardwaarde van 30 seconden gekozen. De time-out kan worden ingesteld op maximaal 230 seconden en minimaal 1 seconde. |
| batchSize | (Optioneel) Geeft aan hoeveel "gegevensrecords" (zie _JSON_ payload-structuur hieronder) per API-aanroep worden verzonden. Als dit niet is ingesteld, wordt een standaardwaarde van 1000 gekozen. We raden u aan gebruik te maken van deze parameter om een geschikte afweging te maken tussen indexering van doorvoer en belasting op uw API |
| mateParallelïsme | (Optioneel) Wanneer opgegeven, geeft het aantal oproepen aan dat de indexer parallel aan het eindpunt dat u hebt opgegeven. U deze waarde verlagen als uw eindpunt niet onder een te hoge aanvraagbelasting staat, of als uw eindpunt meer aanvragen kan accepteren en u de prestaties van de indexer wilt verhogen.  Als dit niet is ingesteld, wordt een standaardwaarde van 5 gebruikt. De mateParallelisme kan worden ingesteld op maximaal 10 en een minimum van 1. |

## <a name="skill-inputs"></a>Vaardigheidsingangen

Er zijn geen "vooraf gedefinieerde" ingangen voor deze vaardigheid. U een of meer velden kiezen die al beschikbaar zijn op het moment van de uitvoering van deze vaardigheid als invoer en de _JSON-payload_ die naar de Web API wordt verzonden, heeft verschillende velden.

## <a name="skill-outputs"></a>Vaardigheidsuitvoer

Er zijn geen "vooraf gedefinieerde" uitgangen voor deze vaardigheid. Afhankelijk van het antwoord dat uw web-API retourneert, voegt u uitvoervelden toe zodat ze kunnen worden opgehaald uit het _JSON-antwoord._


## <a name="sample-definition"></a>Voorbeelddefinitie

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Json-structuur voor voorbeeldinvoer

Deze _JSON-structuur_ vertegenwoordigt de payload die naar uw web-API wordt verzonden.
Het zal altijd volgen deze beperkingen:

* De entiteit op het `values` hoogste niveau wordt aangeroepen en zal een array van objecten zijn. Het aantal van dergelijke objecten zal hoogstens de`batchSize`
* Elk object `values` in de array heeft
    * Een `recordId` eigenschap die een **unieke** tekenreeks is, die wordt gebruikt om die record te identificeren.
    * Een `data` eigenschap die een _JSON-object_ is. De velden `data` van de eigenschap komen overeen `inputs` met de 'namen' die zijn opgegeven in het gedeelte van de vaardigheidsdefinitie. De waarde van deze velden `source` komt uit de velden (die afkomstig kunnen zijn van een veld in het document of mogelijk van een andere vaardigheid)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Json-structuur voor voorbeelduitvoer

De 'uitvoer' komt overeen met het antwoord dat is geretourneerd vanuit uw web-API. De Web API mag alleen een _JSON-payload_ `Content-Type` retourneren (geverifieerd door naar de antwoordkop te kijken) en moet voldoen aan de volgende beperkingen:

* Er moet een entiteit op `values` het hoogste niveau worden aangeroepen die een reeks objecten moet zijn.
* Het aantal objecten in de array moet hetzelfde zijn als het aantal objecten dat naar de web-API wordt verzonden.
* Elk object moet beschikken over:
   * Een `recordId` eigenschap
   * Een `data` eigenschap, dat is een object waar de velden zijn `output` verrijkingen overeenkomen met de "namen" in de en waarvan de waarde wordt beschouwd als de verrijking.
   * Een `errors` eigenschap, een array met eventuele fouten die worden aangetroffen die worden toegevoegd aan de uitvoeringsgeschiedenis van de indexer. Deze eigenschap is vereist, `null` maar kan een waarde hebben.
   * Een `warnings` eigenschap, een array met eventuele waarschuwingen die worden toegevoegd aan de uitvoeringsgeschiedenis van de indexer. Deze eigenschap is vereist, `null` maar kan een waarde hebben.
* De objecten `values` in de array hoeven niet in dezelfde `values` volgorde te staan als de objecten in de array die als een aanvraag naar de web-API worden verzonden. Echter, `recordId` het wordt gebruikt voor correlatie, zodat `recordId` elke record in het antwoord met een die geen deel uitmaakte van de oorspronkelijke aanvraag voor de Web API zal worden verwijderd.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Foutgevallen
Naast dat uw Web API niet beschikbaar is of het verzenden van niet-succesvolle statuscodes worden de volgende gevallen als foutieve gevallen beschouwd:

* Als de web-API een successtatuscode retourneert, maar het antwoord aangeeft dat dit niet `application/json` het is, wordt het antwoord als ongeldig beschouwd en worden er geen verrijkingen uitgevoerd.
* Als er **ongeldige** `recordId` (met niet in de oorspronkelijke aanvraag of `values` met dubbele waarden) records in de antwoordarray zijn, wordt er geen verrijking uitgevoerd voor **die** records.

In gevallen waarin de web-API niet beschikbaar is of een HTTP-fout retourneert, wordt een vriendelijke fout met alle beschikbare details over de HTTP-fout toegevoegd aan de uitvoeringsgeschiedenis van de indexer.

## <a name="see-also"></a>Zie ook

+ [Een vaardighedenset definiëren](cognitive-search-defining-skillset.md)
+ [Aangepaste vaardigheid toevoegen aan een AI-verrijkingspijplijn](cognitive-search-custom-skill-interface.md)
+ [Voorbeeld: Een aangepaste vaardigheid maken voor AI-verrijking](cognitive-search-create-custom-skill-example.md)
