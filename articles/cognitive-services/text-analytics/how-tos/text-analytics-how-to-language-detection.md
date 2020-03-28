---
title: Taal detecteren met de TEXT Analytics REST API
titleSuffix: Azure Cognitive Services
description: Taal detecteren met behulp van de TEXT Analytics REST API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: d34f3a03e1bcd35c270d13c4dda57d0394a36e4b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70387793"
---
# <a name="example-detect-language-with-text-analytics"></a>Voorbeeld: Taal detecteren met Text Analytics

De functie [Taaldetectie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) van de AZURE Text Analytics REST API evalueert tekstinvoer voor elk document en retourneert taal-id's met een score die de sterkte van de analyse aangeeft.

Deze mogelijkheid is handig voor inhoudsarchieven die willekeurige tekst verzamelen, waarin de taal onbekend is. U kunt de resultaten van deze analyse parseren om te bepalen welke taal wordt gebruikt in het ingevoerde document. Het antwoord geeft ook een score die het vertrouwen van het model weerspiegelt. De scorewaarde ligt tussen 0 en 1.

De functie Taaldetectie kan een breed scala aan talen, varianten, dialecten en sommige regionale of culturele talen detecteren. De exacte lijst met talen voor deze functie wordt niet gepubliceerd.

Als u inhoud hebt uitgedrukt in een minder vaak gebruikte taal, u de functie Taaldetectie proberen om te zien of een code wordt geretourneerd. Het antwoord voor talen die niet `unknown`kunnen worden gedetecteerd, is .

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor taaldetectie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

## <a name="preparation"></a>Voorbereiding

U moet JSON-documenten in deze indeling hebben: ID en tekst.

De documentgrootte moet minder dan 5.120 tekens per document bevatten. U maximaal 1.000 items (ID's) per collectie hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voorbeeld is een voorbeeld van inhoud die u indienen voor taaldetectie:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Stap 1: Structuur van de aanvraag

Zie De API voor [Text Analytics aanroepen](text-analytics-how-to-call-api.md)voor meer informatie over de definitie van aanvragen. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een POST-aanvraag. Zie de [API voor taaldetectie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)voor deze aanvraag om de API voor api voor taaldetectie te bekijken.

+ Het HTTP-eindpunt voor taaldetectie instellen. Gebruik een Text Analytics-bron op Azure of een geinstantiëerde [Text Analytics-container.](text-analytics-how-to-install-containers.md) U moet `/text/analytics/v2.1/languages` opnemen in de URL. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Stel een aanvraagkop in om de [toegangssleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics-bewerkingen op te nemen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: POST het verzoek

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.


## <a name="step-3-view-the-results"></a>Stap 3: Bekijk de resultaten

Alle POST-aanvragen retourneren een JSON-geformatteerd antwoord met de id's en gedetecteerde eigenschappen.

Uitvoer wordt onmiddellijk geretourneerd. U de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u gebruiken om de gegevens te sorteren, zoeken en manipuleren.

Resultaten voor de voorbeeldaanvraag moeten eruitzien als de volgende JSON. Merk op dat het één document is met meerdere items. Uitvoer is in het Engels. Taal-id's zijn onder andere een beschrijvende naam en een taalcode in [ISO 639-1](https://www.iso.org/standard/22109.html) indeling.

Een positief score van 1.0 staat voor het hoogst mogelijke vertrouwensniveau van de analyse.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Niet-eenduidige inhoud

In sommige gevallen kan het moeilijk zijn om talen te disambiguate op basis van de input. U `countryHint` de parameter gebruiken om een landcode van 2 letters op te geven. Standaard gebruikt de API de 'US' als standaardlandHint, om dit gedrag te verwijderen kunt `countryHint = ""` u deze parameter opnieuw instellen door deze waarde in te stellen op lege tekenreeks.

"Onmogelijk" is bijvoorbeeld gemeenschappelijk voor zowel Engels als Frans en als het antwoord met beperkte context wordt gegeven, wordt het antwoord gebaseerd op de hint van het "Amerikaanse" land. Als de tekst uit Frankrijk afkomstig is, kan dat als hint worden gegeven.

**Invoer**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

De dienst heeft nu extra context om een beter oordeel te vellen: 

**Output**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Als de analyzer de invoer niet kan `(Unknown)`ontschepen, wordt deze geretourneerd. Een voorbeeld is als u een tekstblok indient dat uitsluitend uit Arabische cijfers bestaat.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Gemengde inhoud

Gemengde inhoud binnen hetzelfde document retourneert de taal met de grootste weergave in de inhoud, maar met een lagere positieve beoordeling. De rating weerspiegelt de marginale sterkte van de beoordeling. De invoer in het volgende voorbeeld is een combinatie van Engels, Spaans en Frans. De analyzer telt tekens in elk segment om te bepalen van de overheersende taal.

**Invoer**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Output**

De resulterende output bestaat uit de overheersende taal, met een score van minder dan 1,0, wat wijst op een zwakker niveau van vertrouwen.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom voor taaldetectie geleerd met behulp van Text Analytics in Azure Cognitive Services. De volgende punten werden toegelicht en aangetoond:

+ [Taaldetectie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) is beschikbaar voor een breed scala aan talen, varianten, dialecten en sommige regionale of culturele talen.
+ JSON-documenten in de aanvraaginstantie bevatten een identiteitsbewijs en tekst.
+ Het POST-verzoek `/languages` is een eindpunt met behulp van een gepersonaliseerde [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ Responsuitvoer bestaat uit taal-id's voor elke document-id. De uitvoer kan worden gestreamd naar elke app die JSON accepteert. Voorbeeld-apps zijn Excel en Power BI, om er maar een paar te noemen.

## <a name="see-also"></a>Zie ook

 [Overzicht van Text Analytics](../overview.md) [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
 [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gevoel analyseren](text-analytics-how-to-sentiment-analysis.md)
