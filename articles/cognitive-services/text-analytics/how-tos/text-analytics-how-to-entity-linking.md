---
title: Entiteits herkenning gebruiken met de Text Analytics-API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het identificeren en dubbel zinnigheid van de identiteit van een entiteit gevonden in tekst met de Text Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 11/21/2019
ms.author: aahi
ms.openlocfilehash: ae5222dcd05740ecb9747037b315c4e920b3eabd
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326625"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Benoemde entiteits herkenning gebruiken in Text Analytics

De [benoemde entiteit herkennings-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) maakt ongestructureerde tekst en voor elk JSON-document wordt een lijst met disambiguated-entiteiten geretourneerd met koppelingen naar meer informatie op het web (Wikipedia en Bing).

## <a name="entity-linking-and-named-entity-recognition"></a>Entiteits koppelingen en benoemde entiteits herkenning

Het Text Analytics `entities`-eind punt ondersteunt zowel entiteits herkenning (NER) als entiteits koppelingen.

### <a name="entity-linking"></a>Entiteiten koppelen
Entiteit koppelen is de mogelijkheid om de identiteit van een entiteit te identificeren en dubbel zinnigheid die in tekst is gevonden (bijvoorbeeld om te bepalen of de Mars wordt gebruikt als planeet of als Romeins niet van War). Voor dit proces moet de aanwezigheid van een Knowledge Base waaraan herkende entiteiten zijn gekoppeld, worden gebruikt als de Knowledge Base voor het `entities`-eind punt Text Analytics.

### <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)
Named entity Recognition (NER) is de mogelijkheid om verschillende entiteiten in tekst te identificeren en deze te categoriseren in vooraf gedefinieerde klassen of typen. 

## <a name="named-entity-recognition-v3-public-preview"></a>Named entity Recognition v3 open bare preview

De volgende versie van named entity Recognition is nu beschikbaar voor open bare preview. Het biedt updates voor zowel entiteits koppelingen als benoemde entiteits herkenning. Probeer het met behulp van de [API-test console](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral).

:::row:::
    :::column span="":::
        **Functie**
    :::column-end:::
    ::: column span="":::
        **Beschrijving** 
    :::column-end:::
:::row-end:::
<!-- expanded types and subtypes row-->
:::row:::
    :::column span="":::
        Uitgebreide entiteits typen en subtypen
    :::column-end:::
    :::column span="":::
     Uitgebreide classificatie en detectie voor verschillende typen entiteit met een naam.
    :::column-end:::
:::row-end:::
<!-- separate endpoints row-->
:::row:::
    :::column span="":::
        Afzonderlijke aanvraag eindpunten 
    :::column-end:::
    :::column span="":::
        Afzonderlijke eind punten voor het verzenden van entiteits koppelings-en NER-aanvragen.
    :::column-end:::
:::row-end:::
<!-- model-version row -->
:::row:::
    :::column span="":::
        `model-version` para meter
    :::column-end:::
    :::column span="":::
        Een optionele para meter voor het kiezen van een versie van het Text Analytics model. Momenteel is alleen het standaard model beschikbaar voor gebruik.
    :::column-end:::
:::row-end:::

### <a name="entity-types"></a>Entiteitstypen

Named entity Recognition V3 biedt uitgebreide detectie over meerdere typen. Op dit moment kan NER v3 de volgende categorieën entiteiten herkennen. Zie het artikel type met de [naam entiteit](../named-entity-types.md) voor een gedetailleerde lijst met ondersteunde entiteiten en talen.

* Algemeen
* Persoonlijke gegevens 

### <a name="request-endpoints"></a>Eind punten van aanvraag

Met named entity Recognition v3 worden afzonderlijke eind punten gebruikt voor NER en aanvragen voor entiteits koppelingen. Gebruik een URL-indeling hieronder op basis van uw aanvraag:

NER
* Algemene entiteiten-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Persoonlijke informatie-entiteiten-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entiteit koppelen
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Model versie beheer

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

## <a name="supported-types-for-named-entity-recognition-v2"></a>Ondersteunde typen voor de herkenning van benoemde entiteiten v2

> [!NOTE]
> De volgende entiteiten worden ondersteund door de named entity Recognition (NER) versie 2. [Ner v3](#named-entity-recognition-v3-public-preview) bevindt zich in de open bare preview en breidt het aantal en de diepte van de entiteiten die in tekst worden herkend, aanzienlijk uit.   

| Type  | SubType | Voorbeeld |
|:-----------   |:------------- |:---------|
| Person        | N.v.t.\*         | "Jeff", "Bill Gates"     |
| Locatie      | N.v.t.\*         | Redmond, Washington; Parijs  |
| Organisatie  | N.v.t.\*         | Microsoft   |
| Aantal      | Aantal        | 6, zes     |
| Aantal      | Percentage    | 50%, vijftig procent|
| Aantal      | Rangtelwoord       | 2e, tweede     |
| Aantal      | Leeftijd           | "90 dag oud", "30 jaar oud"    |
| Aantal      | Valuta      | $ 10,99     |
| Aantal      | Dimensie     | 10 mijl, 40 cm     |
| Aantal      | Temperatuur   | 32 graden    |
| DateTime      | N.v.t.\*         | 18:30 uur, 4 februari 2012      |
| DateTime      | Date          | 2 mei 2017, 05-02-2017   |
| DateTime      | Time          | "8 a.m.", "8:00"  |
| DateTime      | Datumbereik     | 2 mei tot 5 mei    |
| DateTime      | Tijdsbereik     | 18.00 uur tot 19.00 uur     |
| DateTime      | Duur      | 1 minuut en 45 seconden   |
| DateTime      | Instellen           | elke dinsdag     |
| URL           | N.v.t.\*         | "https:\//www.bing.com"    |
| Email         | N.v.t.\*         | "support@contoso.com" |
| Telefoon nummer VS  | N.v.t.\*         | (Alleen telefoon nummers in de VS) "(312) 555-0176" |
| IP-adres    | N.v.t.\*         | "10.0.0.100" |

\* afhankelijk van de invoer en geëxtraheerde entiteiten, kunnen bepaalde entiteiten de `SubType`weglaten.  Alle ondersteunde entiteits typen die worden weer gegeven, zijn alleen beschikbaar voor de talen Engels, Chinees, vereenvoudigd, Duits en Spaans.

### <a name="language-support"></a>Taalondersteuning

Voor het gebruik van entiteits koppeling in verschillende talen moet u in elke taal een bijbehorende kennis database gebruiken. Voor entiteits koppeling in Text Analytics betekent dit dat elke taal die wordt ondersteund door het `entities` eind punt een koppeling maakt naar de bijbehorende Wikipedia-verzameling in die taal. Aangezien de grootte van corpora varieert tussen talen, wordt verwacht dat het intrekken van de entiteits koppelingen ook verschillen. Zie het artikel [language support (Engelstalig)](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) voor meer informatie.

## <a name="preparation"></a>Voorbereiding

U moet JSON-documenten hebben met de volgende indeling: ID, tekst, taal

Voor ondersteunde talen raadpleegt u [deze lijst](../text-analytics-supported-languages.md).

De documentgrootte moet minder dan 5120 tekens per document bevatten en u kunt maximaal 1000 items (id's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend. Het volgende voor beeld is een illustratie van inhoud die u kunt verzenden naar het einde van de entiteit koppeling.

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

## <a name="step-1-structure-the-request"></a>Stap 1: Structuur van de aanvraag

Meer informatie over de definitie van de aanvraag kunt u vinden in [De Text Analytics-API aanroepen](text-analytics-how-to-call-api.md). De volgende punten zijn voor uw gemak opnieuw geformuleerd:

+ Maak een **POST**-aanvraag. Raadpleeg de API-documentatie voor deze aanvraag: [entities-API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

+ Stel het HTTP-eind punt voor de extractie van sleutel woordgroepen in met behulp van een Text Analytics bron in azure of een geïnstantieerd [Text Analytics container](text-analytics-how-to-install-containers.md). U moet `/text/analytics/v2.1/entities`toevoegen. Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`.

+ Stel een aanvraag header in voor [het toevoegen van de toegangs sleutel](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) voor Text Analytics bewerkingen.

+ Verstrek in de hoofdtekst van de aanvraag de JSON-documentenverzameling die u hebt voorbereid voor deze analyse

> [!Tip]
> Gebruik [Postman](text-analytics-how-to-call-api.md) of open de **API-testconsole** in de [documentatie](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634) om de aanvraag te structureren en POST deze in de service.

## <a name="step-2-post-the-request"></a>Stap 2: De aanvraag posten

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
