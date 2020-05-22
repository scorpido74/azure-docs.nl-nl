---
title: Taal detecteren met de Text Analytics REST API
titleSuffix: Azure Cognitive Services
description: Taal detecteren met behulp van de Text Analytics REST API van Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 58f2dc39c185e158a2b4b1b5e73b6b7d589c8c03
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745575"
---
# <a name="example-detect-language-with-text-analytics"></a>Voor beeld: taal detecteren met Text Analytics

De [taaldetectie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) functie van de Azure Text Analytics rest API de tekst invoer voor elk document evalueren en taal-id's retourneert met een score die de sterkte van de analyse aangeeft.

Deze mogelijkheid is handig voor inhoudsarchieven die willekeurige tekst verzamelen, waarin de taal onbekend is. U kunt de resultaten van deze analyse parseren om te bepalen welke taal wordt gebruikt in het ingevoerde document. Het antwoord retourneert ook een score die het vertrouwen van het model weergeeft. De waarde van de Score ligt tussen 0 en 1.

Met de functie Taaldetectie kunt u een breed scala aan talen, varianten, dialecten en bepaalde regionale of culturele talen detecteren. De exacte lijst met talen voor deze functie wordt niet gepubliceerd.

Als er inhoud in een minder vaak gebruikte taal wordt weer gegeven, kunt u de functie Taaldetectie proberen om te zien of er een code wordt geretourneerd. Het antwoord op talen dat niet kan worden gedetecteerd is `unknown` .

> [!TIP]
> Text Analytics biedt ook een Docker-containerinstallatiekopie op basis van Linux voor taaldetectie. U kunt de [Text Analytics-container dus dicht bij uw gegevens installeren en uitvoeren](text-analytics-how-to-install-containers.md).

## <a name="preparation"></a>Voorbereiding

U moet JSON-documenten hebben met de volgende indeling: ID en tekst.

De document grootte moet kleiner zijn dan 5.120 tekens per document. U kunt Maxi maal 1.000 items per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voor beeld is een voor beeld van inhoud die u kunt verzenden voor taal detectie:

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

Zie [de Text Analytics-API aanroepen](text-analytics-how-to-call-api.md)voor meer informatie over de definitie van de aanvraag. De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een POST-aanvraag. Als u de API-documentatie voor deze aanvraag wilt bekijken, raadpleegt u de [taaldetectie-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Het HTTP-eindpunt voor taaldetectie instellen. Gebruik een Text Analytics resource in azure of een geïnstantieerd [Text Analytics container](text-analytics-how-to-install-containers.md). U moet `/text/analytics/v2.1/languages` in de URL toevoegen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/languages`.

+ Stel een aanvraag header in voor het toevoegen van de [toegangs sleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics bewerkingen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse.

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: de aanvraag plaatsen

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor meer informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.


## <a name="step-3-view-the-results"></a>Stap 3: de resultaten weer geven

Alle POST-aanvragen retour neren een reactie in JSON-indeling met de Id's en gedetecteerde eigenschappen.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of de uitvoer opslaan in een bestand op het lokale systeem. Importeer vervolgens de uitvoer in een toepassing die u kunt gebruiken om de gegevens te sorteren, te zoeken en te bewerken.

Resultaten voor de voorbeeldaanvraag moeten eruitzien als de volgende JSON. U ziet dat het één document is met meerdere items. Uitvoer is in het Engels. Taal-id's zijn onder andere een beschrijvende naam en een taalcode in [ISO 639-1](https://www.iso.org/standard/22109.html) indeling.

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

In sommige gevallen kan het lastig zijn om talen te dubbel zinnigheid op basis van de invoer. U kunt de `countryHint` para meter gebruiken om een land-of regio code van twee letters op te geven. Standaard gebruikt de API de standaard countryHint om dit gedrag te verwijderen, kunt u deze para meter opnieuw instellen door deze waarde in te stellen op een lege teken reeks `countryHint = ""` .

"Onmogelijk" is bijvoorbeeld gebruikelijk voor Engels en Frans, en als dit wordt gegeven met een beperkte context, wordt het antwoord gebaseerd op de ' VS ' land/regio-hint. Als de tekst uit Frankrijk afkomstig is, kan dat als hint worden gegeven.

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

Als de analyse functie de invoer niet kan parseren, wordt geretourneerd `(Unknown)` . Een voor beeld is als u een tekst blok verzendt dat uitsluitend uit Arabische cijfers bestaat.

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

### <a name="mixed-language-content"></a>Inhoud in gemengde taal

Gemengde inhoud in hetzelfde document retourneert de taal met de grootste weer gave in de inhoud, maar met een lagere positieve beoordeling. De classificatie weerspiegelt de marginale sterkte van de evaluatie. De invoer in het volgende voorbeeld is een combinatie van Engels, Spaans en Frans. De analyzer telt tekens in elk segment om te bepalen van de overheersende taal.

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

De resulterende uitvoer bestaat uit de meest voorkomende taal, met een Score van minder dan 1,0, wat een zwakker vertrouwens niveau aangeeft.

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

In dit artikel hebt u concepten en werk stromen geleerd voor taal detectie door gebruik te maken van Text Analytics in azure Cognitive Services. De volgende punten zijn uitgelegd en aangetoond:

+ [Taal detectie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) is beschikbaar voor een breed scala aan talen, varianten, dialecten en enkele regionale of culturele talen.
+ JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID en tekst.
+ De POST-aanvraag is naar een `/languages` eind punt met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
+ De reactie-uitvoer bestaat uit taal-id's voor elke document-ID. De uitvoer kan worden gestreamd naar alle apps die JSON accepteren. Voor beeld-apps zijn Excel en Power BI, om een paar te noemen.

## <a name="see-also"></a>Zie ook

 [Overzicht van Text Analytics](../overview.md) [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
 [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gevoel analyseren](text-analytics-how-to-sentiment-analysis.md)
