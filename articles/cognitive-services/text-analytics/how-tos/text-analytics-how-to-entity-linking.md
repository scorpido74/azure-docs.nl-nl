---
title: Entiteits herkenning gebruiken met de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het herkennen van entiteiten met behulp van de Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 93d5b3de47ec0b3c0494589da0baf87f91a0233a
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390262"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Benoemde entiteits herkenning gebruiken in Text Analytics

De [benoemde entiteit herkennings-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) maakt ongestructureerde tekst en voor elk JSON-document wordt een lijst met disambiguated-entiteiten geretourneerd met koppelingen naar meer informatie op het web (Wikipedia en Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Entiteits koppelingen en benoemde entiteits herkenning

Het `entities` eind punt van de Text Analytics ondersteunt zowel ner (named entity Recognition) als entiteits koppeling.

### <a name="entity-linking"></a>Entiteiten koppelen
Entiteit koppelen is de mogelijkheid om de identiteit van een entiteit te identificeren en dubbel zinnigheid die in tekst is gevonden (bijvoorbeeld om te bepalen of de Mars wordt gebruikt als planeet of als Romeins niet van War). Voor dit proces moet de aanwezigheid van een Knowledge Base waaraan herkende entiteiten zijn gekoppeld, worden gebruikt als de Knowledge Base voor het `entities` eind punt Text Analytics.

### <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)
Herkenning van benoemde entiteiten (NER) is de mogelijkheid om verschillende entiteiten in tekst te identificeren en deze te categoriseren in vooraf gedefinieerde klassen. De ondersteunde klassen van entiteiten worden hieronder weer gegeven.

In Text Analytics [versie 2,1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)zijn zowel entiteits koppelingen als benoemde entiteits herkenning (ner) beschikbaar voor verschillende talen. Zie het artikel [language support (Engelstalig)](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) voor meer informatie.

### <a name="language-support"></a>Taalondersteuning

Voor het gebruik van entiteits koppeling in verschillende talen moet u in elke taal een bijbehorende kennis database gebruiken. Voor entiteits koppeling in Text Analytics betekent dit dat elke taal die wordt ondersteund door `entities` het eind punt, wordt gekoppeld aan de bijbehorende Wikipedia-verzameling in die taal. Aangezien de grootte van corpora varieert tussen talen, wordt verwacht dat het intrekken van de entiteits koppelingen ook verschillen.

## <a name="supported-types-for-named-entity-recognition"></a>Ondersteunde typen voor herkenning van benoemde entiteiten

| type  | SubType | Voorbeeld |
|:-----------   |:------------- |:---------|
| Person        | N.V.T.\*         | "Jeff", "Bill Gates"     |
| Location      | N.V.T.\*         | Redmond, Washington; Parijs  |
| Organisatie  | N.V.T.\*         | "Microsoft"   |
| Hoeveelheid      | Aantal        | "6", "six"     |
| Hoeveelheid      | Percentage    | 50%, vijftig procent|
| Hoeveelheid      | Rangtelwoord       | 2e, tweede     |
| Hoeveelheid      | Nummerbereik   | 4 tot 8     |
| Hoeveelheid      | Leeftijd           | "90 dag oud", "30 jaar oud"    |
| Hoeveelheid      | Currency      | $ 10,99     |
| Hoeveelheid      | Dimensie     | 10 mijl, 40 cm     |
| Hoeveelheid      | Temperatuur   | 32 graden    |
| DateTime      | N.V.T.\*         | 18:30 uur, 4 februari 2012      |
| DateTime      | Date          | 2 mei 2017, 05-02-2017   |
| DateTime      | Time          | "8 a.m.", "8:00"  |
| DateTime      | DateRange     | 2 mei tot 5 mei    |
| DateTime      | TimeRange     | 18.00 uur tot 19.00 uur     |
| DateTime      | Duration      | 1 minuut en 45 seconden   |
| DateTime      | Set           | elke dinsdag     |
| DateTime      | Tijdzone      |    |
| URL           | N.V.T.\*         | "https:\//www.bing.com"    |
| Email         | N.V.T.\*         | "support@contoso.com" |

\*Afhankelijk van de invoer en geëxtraheerde entiteiten kunnen bepaalde entiteiten de `SubType`niet weglaten.  Alle ondersteunde entiteits typen die worden weer gegeven, zijn alleen beschikbaar voor Engels, vereenvoudigd Chinees, Frans, Duits en Spaans.



## <a name="preparation"></a>Voorbereiding

U moet JSON-documenten hebben in deze indeling: ID, tekst, taal

Voor ondersteunde talen raadpleegt u [deze lijst](../text-analytics-supported-languages.md).

De documentgrootte moet minder dan maximaal 5120 tekens per document zijn, en u kunt maximaal 1000 items (id's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voor beeld is een illustratie van inhoud die u kunt verzenden naar het einde van de entiteit koppeling.

```json
    {
        "documents": [
            {
                "id": "1",
                "language": "en",
                "text": "Jeff bought three dozen eggs because there was a 50% discount."
            },
            {
                "id": "2",
                "language": "en",
                "text": "The Great Depression began in 1929. By 1933, the GDP in America fell by 25%."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Stap 1: Structureer de aanvraag

Meer informatie over de definitie van de aanvraag kunt u vinden in [De Text Analytics-API aanroepen](text-analytics-how-to-call-api.md). De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een **POST**-aanvraag. Bekijk de API-documentatie voor deze aanvraag: [API voor entiteiten](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Stel het HTTP-eind punt voor de extractie van sleutel woordgroepen in met behulp van een Text Analytics bron in azure of een geïnstantieerd [Text Analytics container](text-analytics-how-to-install-containers.md). U moet het `/text/analytics/v2.1/entities`volgende toevoegen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Stel een aanvraag header in voor [het toevoegen van de toegangs sleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics bewerkingen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

Terughalen als de service staatloos is. Er worden geen gegevens opgeslagen in uw account. Resultaten worden onmiddellijk in het antwoord geretourneerd.

## <a name="step-3-view-results"></a>Stap 3: Resultaten weergeven

Alle POST-verzoeken retourneren een ingedeeld JSON-antwoord met de id's en gedetecteerde eigenschappen.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren.

Een voor beeld van de uitvoer voor entiteits koppeling wordt volgende weer gegeven:

```json
    {
        "Documents": [
            {
                "Id": "1",
                "Entities": [
                    {
                        "Name": "Jeff",
                        "Matches": [
                            {
                                "Text": "Jeff",
                                "Offset": 0,
                                "Length": 4
                            }
                        ],
                        "Type": "Person"
                    },
                    {
                        "Name": "three dozen",
                        "Matches": [
                            {
                                "Text": "three dozen",
                                "Offset": 12,
                                "Length": 11
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50",
                        "Matches": [
                            {
                                "Text": "50",
                                "Offset": 49,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "50%",
                        "Matches": [
                            {
                                "Text": "50%",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            },
            {
                "Id": "2",
                "Entities": [
                    {
                        "Name": "Great Depression",
                        "Matches": [
                            {
                                "Text": "The Great Depression",
                                "Offset": 0,
                                "Length": 20
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Great Depression",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Great_Depression",
                        "BingId": "d9364681-98ad-1a66-f869-a3f1c8ae8ef8"
                    },
                    {
                        "Name": "1929",
                        "Matches": [
                            {
                                "Text": "1929",
                                "Offset": 30,
                                "Length": 4
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "By 1933",
                        "Matches": [
                            {
                                "Text": "By 1933",
                                "Offset": 36,
                                "Length": 7
                            }
                        ],
                        "Type": "DateTime",
                        "SubType": "DateRange"
                    },
                    {
                        "Name": "Gross domestic product",
                        "Matches": [
                            {
                                "Text": "GDP",
                                "Offset": 49,
                                "Length": 3
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "Gross domestic product",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/Gross_domestic_product",
                        "BingId": "c859ed84-c0dd-e18f-394a-530cae5468a2"
                    },
                    {
                        "Name": "United States",
                        "Matches": [
                            {
                                "Text": "America",
                                "Offset": 56,
                                "Length": 7
                            }
                        ],
                        "WikipediaLanguage": "en",
                        "WikipediaId": "United States",
                        "WikipediaUrl": "https://en.wikipedia.org/wiki/United_States",
                        "BingId": "5232ed96-85b1-2edb-12c6-63e6c597a1de",
                        "Type": "Location"
                    },
                    {
                        "Name": "25",
                        "Matches": [
                            {
                                "Text": "25",
                                "Offset": 72,
                                "Length": 2
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Number"
                    },
                    {
                        "Name": "25%",
                        "Matches": [
                            {
                                "Text": "25%",
                                "Offset": 72,
                                "Length": 3
                            }
                        ],
                        "Type": "Quantity",
                        "SubType": "Percentage"
                    }
                ]
            }
        ],
        "Errors": []
    }
```

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het koppelen van entiteiten met behulp van Text Analytics in Cognitive Services. Samenvatting:

+ Er is een [entiteits-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) beschikbaar voor geselecteerde talen.
+ JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID, tekst en taal code.
+ POST-aanvraag is een `/entities`-eindpunt die een persoonlijke [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) gebruikt die geldig zijn voor uw abonnement.
+ De antwoord uitvoer, die bestaat uit gekoppelde entiteiten (inclusief betrouwbaarheids scores, verschuivingen en webkoppelingen, voor elke document-ID), kan worden gebruikt in elke toepassing

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Text Analytics-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

* [Overzicht van Text Analytics](../overview.md)
* [Veelgestelde vragen](../text-analytics-resource-faq.md)</br>
* [Text Analytics-productpagina](//go.microsoft.com/fwlink/?LinkID=759712)
