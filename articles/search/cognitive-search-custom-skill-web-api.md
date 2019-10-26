---
title: Aangepaste cognitieve Zoek vaardigheid-Azure Search
description: Mogelijkheden van cognitieve Zoek vaardig heden uitbreiden door naar web-Api's aan te roepen
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: fda4f96c2c73c5a2d39435a509afcf654ed77b70
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901320"
---
# <a name="custom-web-api-skill"></a>Aangepaste web API-vaardigheid

Met de **aangepaste web API** -vaardigheid kunt u een cognitieve zoek opdracht uitbreiden door aan te roepen naar een web-API-eind punt dat aangepaste bewerkingen biedt. Net als bij ingebouwde vaardig heden bevat een **aangepaste web-API** -vaardigheid invoer en uitvoer. Afhankelijk van de invoer, ontvangt uw web-API een JSON-nettolading wanneer de Indexeer functie wordt uitgevoerd en voert deze een JSON-nettolading uit als antwoord, samen met de status code geslaagd. Er wordt verwacht dat het antwoord de uitvoer bevat die is opgegeven door uw aangepaste vaardigheid. Elk ander antwoord wordt als een fout beschouwd en er worden geen verrijkingen uitgevoerd.

De structuur van de JSON-nettoladingen wordt verder beschreven in dit document.

> [!NOTE]
> De Indexeer functie voert twee keer opnieuw uit voor bepaalde standaard HTTP-status codes die worden geretourneerd door de Web-API. Deze HTTP-status codes zijn: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Micro soft. skills. custom. WebApiSkill

## <a name="skill-parameters"></a>Vaardigheids parameters

Para meters zijn hoofdletter gevoelig.

| Parameternaam     | Beschrijving |
|--------------------|-------------|
| URI | De URI van de Web-API waarnaar de _JSON_ -nettolading wordt verzonden. Alleen **https** URI-schema is toegestaan |
| httpMethod | De methode die moet worden gebruikt bij het verzenden van de payload. Toegestane methoden zijn `PUT` of `POST` |
| httpHeaders | Een verzameling sleutel-waardeparen waarbij de sleutels koptekst namen en-waarden vertegenwoordigen header waarden die worden verzonden naar uw web-API en de payload. De volgende headers mogen niet voor komen in deze verzameling: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| timeout | Beschrijving Hiermee geeft u de time-out op voor de HTTP-client die de API-aanroep maakt. Deze moet worden ingedeeld als een XSD ' dayTimeDuration-waarde (een beperkte subset van een [ISO 8601 duration](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) -waarde). Bijvoorbeeld `PT60S` 60 seconden. Als deze niet is ingesteld, wordt de standaard waarde van 30 seconden gekozen. De time-out kan worden ingesteld op een maximum van 230 seconden en een minimum van 1 seconde. |
| batchSize | Beschrijving Hiermee wordt aangegeven hoeveel ' gegevens records ' (Zie de structuur van de _JSON_ -nettolading hieronder) per API-aanroep worden verzonden. Als deze niet is ingesteld, wordt de standaard waarde 1000 gekozen. We raden u aan gebruik te maken van deze para meter om een geschikte verhouding te krijgen tussen het door voeren van de indexering en de belasting van uw API |
| degreeOfParallelism | Beschrijving Indien opgegeven, wordt hiermee het aantal aanroepen aangegeven dat de Indexeer functie parallel moet maken aan het eind punt dat u hebt opgegeven. U kunt deze waarde verlagen als uw eind punt met een te hoge belasting van de aanvraag is mislukt of als u het wilt verhogen als uw eind punt meer aanvragen kan accepteren en u de prestaties van de Indexeer functie wilt verhogen.  Als deze niet is ingesteld, wordt de standaard waarde 5 gebruikt. De degreeOfParallelism kan worden ingesteld op een maximum van 10 en een minimum van 1. |

## <a name="skill-inputs"></a>Vaardigheids invoer

Er zijn geen "vooraf gedefinieerde" invoer voor deze vaardigheid. U kunt een of meer velden kiezen die al beschikbaar zijn op het moment dat de uitvoering van deze vaardigheid als invoer wordt uitgevoerd en de _JSON_ -nettolading die naar de Web-API wordt verzonden, verschillende velden heeft.

## <a name="skill-outputs"></a>Vaardigheids uitvoer

Er zijn geen vooraf gedefinieerde uitvoer voor deze vaardigheid. Afhankelijk van de reactie die uw web-API retourneert, voegt u uitvoer velden toe zodat deze kunnen worden opgehaald uit het _JSON_ -antwoord.


## <a name="sample-definition"></a>Voorbeeld definitie

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can count the number of words or characters or lines in text",
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
            "name": "countOf",
            "source": "/document/propertyToCount"
          }
        ],
        "outputs": [
          {
            "name": "count",
            "targetName": "countOfThings"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Voorbeeld structuur voor de invoer van JSON

Deze _JSON_ -structuur vertegenwoordigt de nettolading die wordt verzonden naar uw web-API.
Deze beperkingen worden altijd gevolgd:

* De entiteit op het hoogste niveau heet `values` en is een matrix met objecten. Het aantal objecten is de meeste `batchSize`
* Elk object in de `values` matrix heeft
    * Een `recordId` eigenschap die een **unieke** teken reeks is, die wordt gebruikt om die record te identificeren.
    * Een `data` eigenschap die een _JSON_ -object is. De velden van de eigenschap `data` komen overeen met de namen die zijn opgegeven in de sectie `inputs` van de definitie van de vaardigheid. De waarde van deze velden wordt uit de `source` van die velden (die mogelijk afkomstig zijn uit een veld in het document of mogelijk van een andere vaardigheid)

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "countOf": "words"
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "countOf": "characters"
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world \r\n Hi World",
             "language": "en",
             "countOf": "lines"
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "countOf": null
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Voor beeld van JSON-structuur van uitvoer

De "uitvoer" komt overeen met de reactie die wordt geretourneerd door de Web-API. De Web-API mag alleen een _JSON_ -nettolading retour neren (gecontroleerd door te kijken naar de `Content-Type` reactie header) en moet voldoen aan de volgende beperkingen:

* Er moet een entiteit op het hoogste niveau worden aangeroepen `values` die een matrix met objecten moet zijn.
* Het aantal objecten in de matrix moet hetzelfde zijn als het aantal objecten dat naar de Web-API wordt verzonden.
* Elk object moet het volgende hebben:
   * Een `recordId` eigenschap
   * Een `data` eigenschap, die een object is waarbij de velden verrijkingen zijn die overeenkomen met de ' namen ' in de `output` en waarvan de waarde wordt beschouwd als de verrijking.
   * Een `errors`-eigenschap, een matrix met eventuele fouten die worden toegevoegd aan de uitvoerings geschiedenis van de Indexeer functie. Deze eigenschap is vereist, maar kan een `null`-waarde hebben.
   * Een `warnings`-eigenschap, een matrix met waarschuwingen die worden toegevoegd aan de uitvoerings geschiedenis van de Indexeer functie. Deze eigenschap is vereist, maar kan een `null`-waarde hebben.
* De objecten in de `values` matrix moeten zich niet in dezelfde volg orde besturen als de objecten in de `values` matrix die als een aanvraag voor de Web-API worden verzonden. De `recordId` wordt echter gebruikt voor correlatie, zodat alle records in het antwoord met een `recordId` die geen deel uitmaken van de oorspronkelijke aanvraag voor de Web-API, worden verwijderd.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "Cannot understand what needs to be counted"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "count": 2
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "count": 6
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "count": 11
            },
            "errors": null,
            "warnings": null
        },
    ]
}

```

## <a name="error-cases"></a>Fout cases
Naast de Web-API die niet beschikbaar is, of het verzenden van niet-geslaagde status codes, worden de volgende als foutieve cases beschouwd:

* Als de Web-API een succes status code retourneert, maar het antwoord geeft aan dat deze niet `application/json`, wordt het antwoord beschouwd als ongeldig en worden er geen verrijkingen uitgevoerd.
* Als er **Ongeldige** records zijn (met `recordId` niet in de oorspronkelijke aanvraag of met dubbele waarden) in de antwoord `values` matrix, wordt er geen verrijking uitgevoerd voor **deze** records.

Als de Web-API niet beschikbaar is of een HTTP-fout retourneert, wordt een beschrijvende fout met alle beschik bare Details over de HTTP-fout toegevoegd aan de indexerings geschiedenis.

## <a name="see-also"></a>Zie ook

+ [Power vaardig heden: een opslag plaats met aangepaste vaardig heden](https://aka.ms/powerskills)
+ [Een vaardig heden definiëren](cognitive-search-defining-skillset.md)
+ [Aangepaste vaardigheid toevoegen aan cognitieve zoek functie](cognitive-search-custom-skill-interface.md)
+ [Voor beeld: een aangepaste vaardigheid maken voor cognitieve zoek acties](cognitive-search-create-custom-skill-example.md)