---
title: Taal detecteren met de Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Detecteer taal met de Text Analytics REST API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 05/13/2020
ms.author: aahi
ms.openlocfilehash: b5936d68ba98054eb6d98860c6edfaf12d6736d8
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91710122"
---
# <a name="example-detect-language-with-text-analytics"></a>Voorbeeld: Taal detecteren met Text Analytics

De [Taaldetectie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages)-functie van de Azure Text Analytics REST API evalueert tekstinvoer voor elk document en retourneert de taal-id's met een score die wijst op de sterkte van de analyse.

Deze mogelijkheid is handig voor inhoudsarchieven die willekeurige tekst verzamelen, waarin de taal onbekend is. U kunt de resultaten van deze analyse parseren om te bepalen welke taal wordt gebruikt in het ingevoerde document. Het antwoord retourneert ook een score die overeenkomt met het vertrouwen van het model. De scorewaarde ligt tussen 0 en 1.

Met de functie Taaldetectie kunt u een breed scala aan talen, varianten, dialecten en bepaalde regionale of culturele talen detecteren. De exacte lijst met talen voor deze functie wordt niet gepubliceerd.

Als er inhoud in een minder vaak gebruikte taal wordt weergegeven, kunt u de functie Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord voor talen dat niet kan worden gedetecteerd, is `unknown`.

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor taaldetectie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

## <a name="preparation"></a>Voorbereiding

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

U moet JSON-documenten in deze indeling hebben: id en tekst.

De documenten mogen niet meer dan 5.120 tekens per document bevatten. Een verzameling mag maximaal 1000 items (id's) bevatten. De verzameling is in de hoofdtekst van de aanvraag ingediend. Hier volgt een voorbeeld van de inhoud die u voor taaldetectie kunt indienen:

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

Zie [De Text Analytics-API aanroepen](text-analytics-how-to-call-api.md) voor meer informatie over de definitie van de aanvraag. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een POST-aanvraag. Als u de API-documentatie voor deze aanvraag wilt bekijken, gaat u naar [Taaldetectie-API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Het HTTP-eindpunt voor taaldetectie instellen. Gebruik een Text Analytics-resource in Azure of een geïnstantieerde [Text Analytics-container](text-analytics-how-to-install-containers.md). U moet `/text/analytics/v3.0/languages` in de URL opnemen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Stel een aanvraagheader in om de [toegangssleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor de Text Analytics-bewerkingen op te nemen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie het gedeelte over [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en per seconde kunt verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.


## <a name="step-3-view-the-results"></a>Stap 3: De resultaten bekijken

Alle POST-aanvragen retourneren een ingedeeld JSON-antwoord met de id's en gedetecteerde eigenschappen.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer op het lokale systeem opslaan als een bestand. Vervolgens importeert u de uitvoerput in een toepassing waarin u de gegevens kunt sorteren, doorzoeken en bewerken.

Resultaten voor de voorbeeldaanvraag moeten eruitzien als de volgende JSON. Let op: het is één document met meerdere items. Uitvoer is in het Engels. Taal-id's zijn onder andere een beschrijvende naam en een taalcode in [ISO 639-1](https://www.iso.org/standard/22109.html) indeling.

Een positief score van 1.0 staat voor het hoogst mogelijke vertrouwensniveau van de analyse.

```json
{
    "documents": [
        {
            "id": "1",
            "detectedLanguage": {
                "name": "English",
                "iso6391Name": "en",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "2",
            "detectedLanguage": {
                "name": "Spanish",
                "iso6391Name": "es",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "3",
            "detectedLanguage": {
                "name": "French",
                "iso6391Name": "fr",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "4",
            "detectedLanguage": {
                "name": "Chinese_Simplified",
                "iso6391Name": "zh_chs",
                "confidenceScore": 1.0
            },
            "warnings": []
        },
        {
            "id": "5",
            "detectedLanguage": {
                "name": "Russian",
                "iso6391Name": "ru",
                "confidenceScore": 1.0
            },
            "warnings": []
        }
    ],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

### <a name="ambiguous-content"></a>Niet-eenduidige inhoud

In sommige gevallen kan het lastig zijn om talen ondubbelzinnig te karakteriseren op basis van de invoer. U kunt parameter `countryHint` gebruiken om een land- of regionummer van twee letters op te geven. Standaard gebruikt de API de standaard-countryHint US. Als u dit gedrag wilt verwijderen, kunt u deze parameter opnieuw instellen door deze waarde in te stellen op de lege tekenreeks `countryHint = ""`.

'Impossible' is bijvoorbeeld gebruikelijk voor zowel Engels als Frans en als dit wordt opgegeven met beperkte context, is de reactie gebaseerd op de land/regio-hint 'VS'. Als de tekst uit Frankrijk afkomstig is, kan dat als hint worden gegeven.

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

De service heeft nu aanvullende context om een betere beslissing te nemen: 

**Uitvoer**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "confidenceScore": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "confidenceScore": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Als de analysefunctie de invoer niet kan parseren, wordt `(Unknown)`geretourneerd. Een voorbeeld is als u een tekstblok verzendt dat uitsluitend uit Arabische cijfers bestaat.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "confidenceScore": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Inhoud in gemengde taal

Inhoud in gemengde talen binnen hetzelfde document retourneert de taal met de grootste weergave in de inhoud, maar met een lagere positieve classificatie. De classificatie weerspiegelt de marginale sterkte van de evaluatie. De invoer in het volgende voorbeeld is een combinatie van Engels, Spaans en Frans. De analyzer telt tekens in elk segment om te bepalen van de overheersende taal.

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

**Uitvoer**

De resulterende uitvoer bestaat uit de overheersende taal, met een score van minder dan 1,0 die wijst op een zwakkere mate van betrouwbaarheid.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "confidencescore": 0.94
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor taaldetectie met behulp van de Text Analytics in Azure Cognitive Services. De volgende punten zijn uitgelegd en gedemonstreerd:

+ [Taaldetectie](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) is beschikbaar voor een breed scala aan talen, varianten, dialecten en bepaalde regionale of culturele talen.
+ JSON-documenten in de aanvraagbody omvatten een id en tekst.
+ De POST-aanvraag wordt verzonden naar een `/languages`-eindpunt met behulp van een persoonlijke [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) die geldig zijn voor uw abonnement.
+ De reactie-uitvoer bestaat uit taal-id's voor elke document-id. De uitvoer kan worden gestreamd naar alle apps die JSON accepteren. Voorbeeldapps zijn Excel en Power BI, om er een paar te noemen.

## <a name="see-also"></a>Zie ook

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-clientbibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Nieuwe functies](../whats-new.md)
