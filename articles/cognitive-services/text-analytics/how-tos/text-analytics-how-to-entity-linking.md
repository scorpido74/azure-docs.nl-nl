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
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 607b65d6a6893901ce23cd48c277c14209128866
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137973"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Benoemde entiteits herkenning gebruiken in Text Analytics

Met de Text Analytics-API kunt u ongestructureerde tekst afgeven en een lijst met disambiguated-entiteiten retour neren met koppelingen naar meer informatie op het web. De API ondersteunt zowel NER (named entity Recognition) als entiteits koppeling.

### <a name="entity-linking"></a>Entiteiten koppelen

Entiteit koppelen is de mogelijkheid om de identiteit van een entiteit te identificeren en te dubbel zinnigheid die in tekst is gevonden (bijvoorbeeld om te bepalen of een exemplaar van het woord `Mars` verwijst naar de planeet of naar de Romeinse niet van War). Voor dit proces moet de aanwezigheid van een Knowledge Base in de juiste taal worden gekoppeld om herkende entiteiten in de tekst te koppelen. De koppeling van de entiteit maakt gebruik van [Wikipedia](https://www.wikipedia.org/) als deze Knowledge Base.


### <a name="named-entity-recognition-ner"></a>Herkenning van benoemde entiteiten (NER)

Herkenning van benoemde entiteiten (NER) is de mogelijkheid om verschillende entiteiten in tekst te identificeren en deze te categoriseren in vooraf gedefinieerde klassen of typen. Bijvoorbeeld: personen, plaatsen en organisaties.

## <a name="named-entity-recognition-versions-and-features"></a>Erkennings versies en-functies voor benoemde eenheden

De Text Analytics-API biedt twee versies van named entity Recognition-v2 en v3. Versie 3 (open bare preview) biedt meer details in de entiteiten die kunnen worden gedetecteerd en gecategoriseerd.

| Functie                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Methoden voor enkelvoudige en batch-aanvragen                          | X      | X      |
| Basis herkenning van entiteiten in verschillende categorieën              | X      | X      |
| Uitgebreide classificatie voor herkende entiteiten                 |        | X      |
| Afzonderlijke eind punten voor het verzenden van entiteits koppelings-en NER-aanvragen. |        | X      |
| Model versie beheer                                                |        | X      |

Zie [taal ondersteuning](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) voor meer informatie.


#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

### <a name="entity-types"></a>Entiteitstypen

Named entity Recognition V3 biedt uitgebreide detectie over meerdere typen. Op dit moment kan NER v3 de volgende categorieën entiteiten herkennen:

* Algemeen
* Persoonlijke gegevens 

Zie het artikel [ner v3 ondersteunde entiteit types](../named-entity-types.md) voor een gedetailleerde lijst met ondersteunde entiteiten en talen.

### <a name="request-endpoints"></a>Eind punten van aanvraag

Met named entity Recognition v3 worden afzonderlijke eind punten gebruikt voor NER en aanvragen voor entiteits koppelingen. Gebruik een URL-indeling hieronder op basis van uw aanvraag:

NER
* Algemene entiteiten-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Persoonlijke gegevens-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entiteit koppelen
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Model versie beheer

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

### <a name="entity-types"></a>Entiteitstypen

> [!NOTE]
> Named entity Recognition (NER) versie 2 ondersteunt alleen de volgende entiteiten. NER V3 bevindt zich in de open bare preview en breidt het aantal en de diepte van de entiteiten die in tekst worden herkend, aanzienlijk uit.   

| Type  | SubType | Voorbeeld |
|:-----------   |:------------- |:---------|
| Person        | N.v.t.\*         | "Jeff", "Bill Gates"     |
| Locatie      | N.v.t.\*         | "Redmond, Washington", "Parijs"  |
| Organisatie  | N.v.t.\*         | "Microsoft"   |
| Aantal      | Aantal        | "6", "six"     |
| Aantal      | Percentage    | "50%", "50 procent"|
| Aantal      | Rang telwoord       | ' 2e ', ' seconde '     |
| Aantal      | Leeftijd           | "90 dag oud", "30 jaar oud"    |
| Aantal      | Valuta      | "$10,99"     |
| Aantal      | Dimensielideigenschap     | "10 mijl", "40 cm"     |
| Aantal      | Temperatuur   | "32 graden"    |
| DateTime      | N.v.t.\*         | "6:17.30 februari 4, 2012"      |
| DateTime      | Date          | "Mei 2e, 2017", "05/02/2017"   |
| DateTime      | Time          | "8 a.m.", "8:00"  |
| DateTime      | DateRange     | "Mag 2e tot vijfde mei"    |
| DateTime      | TimeRange     | "18:00 uur naar 19.00 uur"     |
| DateTime      | Duur      | "1 minuut en 45 seconden"   |
| DateTime      | Instellen           | "elke dinsdag"     |
| URL           | N.v.t.\*         | "https:\//www.bing.com"    |
| Email         | N.v.t.\*         | "support@contoso.com" |
| Telefoon nummer VS  | N.v.t.\*         | (Alleen telefoon nummers in de VS) "(312) 555-0176" |
| IP-adres    | N.v.t.\*         | "10.0.0.100" |

\* afhankelijk van de invoer en geëxtraheerde entiteiten, kunnen bepaalde entiteiten de `SubType`weglaten.  Alle ondersteunde entiteits typen die worden weer gegeven, zijn alleen beschikbaar voor de talen Engels, Chinees, vereenvoudigd, Duits en Spaans.

### <a name="request-endpoints"></a>Eind punten van aanvraag

De benoemde entiteits herkenning v2 gebruikt één eind punt voor NER en aanvragen voor entiteits koppeling:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Een REST API aanvraag verzenden

### <a name="preparation"></a>Voorbereiding

U moet JSON-documenten hebben met de volgende indeling: ID, tekst, taal.

Elk document moet 5.120 tekens lang zijn en u kunt Maxi maal 1.000 items (Id's) per verzameling hebben. De verzameling is in de hoofdtekst van de aanvraag ingediend.

### <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U kunt [postman](text-analytics-how-to-call-api.md) of de **API-test console** in de volgende koppelingen gebruiken om een snelle structuur en verzen ding te sturen. 

> [!NOTE]
> U vindt de sleutel en het eind punt voor uw Text Analytics-resource in azure Portal. Ze bevinden zich op de pagina **snel starten** van de resource, onder **resource beheer**. 

#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview](#tab/version-3)

[Benoemde entiteit herkenning v3-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Versie 3 gebruikt afzonderlijke eind punten voor NER en entiteits koppelings aanvragen. Gebruik een URL-indeling hieronder op basis van uw aanvraag:

NER
* Algemene entiteiten-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Persoonlijke informatie-entiteiten-`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Entiteit koppelen
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

[Naslag Gids voor benoemde entiteits herkenning (NER) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Versie 2 gebruikt het volgende eind punt voor entiteits koppelings-en NER-aanvragen: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Stel een aanvraag header in om uw Text Analytics-API sleutel toe te voegen. Geef in de hoofd tekst van de aanvraag de JSON-documenten op die u hebt voor bereid.

### <a name="example-ner-request"></a>Voor beeld van NER-aanvraag 

Hier volgt een voor beeld van de inhoud die u naar de API zou kunnen verzenden. De aanvraag indeling is hetzelfde voor beide versies van de API.

```json
{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "I had a wonderful trip to Seattle last week."
    }
  ]
}
```

## <a name="post-the-request"></a>Plaats de aanvraag

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevens limieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde kunt verzenden.

De Text Analytics-API is stateless. Er worden geen gegevens in uw account opgeslagen en de resultaten worden onmiddellijk in het antwoord geretourneerd.

## <a name="view-results"></a>Resultaten weergeven

Alle POST-aanvragen retour neren een JSON-indelings antwoord met de eigenschappen id en gedetecteerde entiteit.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren.


#### <a name="version-30-previewtabversion-3"></a>[Versie 3,0-Preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Voor beeld v3-antwoorden

Versie 3 biedt afzonderlijke eind punten voor NER en entiteits koppelingen. De antwoorden voor beide bewerkingen vindt u hieronder.

#### <a name="example-ner-response"></a>Voor beeld van NER-antwoord

```json
{
    "documents": [{
    "id": "1",
    "entities": [{
        "text": "Seattle",
        "type": "Location",
        "offset": 26,
        "length": 7,
        "score": 0.80624294281005859
    }, {
        "text": "last week",
        "type": "DateTime",
        "subtype": "DateRange",
        "offset": 34,
        "length": 9,
        "score": 0.8
    }]
    }],
    "errors": [],
    "modelVersion": "2019-10-01"
}
```

#### <a name="example-entity-linking-response"></a>Voor beeld van entiteit koppelings reactie

```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "text": "Seattle",
        "offset": 26,
        "length": 7,
        "score": 0.15046201222847677
      }],
      "language": "en",
      "id": "Seattle",
      "url": "https://en.wikipedia.org/wiki/Seattle",
      "dataSource": "Wikipedia"
    }]
  }],
  "errors": [],
  "modelVersion": "2019-10-01"
}
```

#### <a name="version-21tabversion-2"></a>[Versie 2,1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Voor beeld van NER v2-antwoord
```json
{
  "documents": [{
    "id": "1",
    "entities": [{
      "name": "Seattle",
      "matches": [{
        "wikipediaScore": 0.15046201222847677,
        "entityTypeScore": 0.80624294281005859,
        "text": "Seattle",
        "offset": 26,
        "length": 7
      }],
      "wikipediaLanguage": "en",
      "wikipediaId": "Seattle",
      "wikipediaUrl": "https://en.wikipedia.org/wiki/Seattle",
      "bingId": "5fbba6b8-85e1-4d41-9444-d9055436e473",
      "type": "Location"
    }, {
      "name": "last week",
      "matches": [{
        "entityTypeScore": 0.8,
        "text": "last week",
        "offset": 34,
        "length": 9
      }],
      "type": "DateTime",
      "subType": "DateRange"
    }]
  }],
  "errors": []
}
```

---

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werk stromen geleerd voor het koppelen van entiteiten met behulp van Text Analytics in Cognitive Services. Samenvatting:

* Herkenning van benoemde entiteiten is beschikbaar voor geselecteerde talen in twee versies.
* JSON-documenten in de hoofd tekst van de aanvraag bevatten een ID, tekst en taal code.
* POST-aanvragen worden verzonden naar een of meer eind punten met behulp van een aangepaste [toegangs sleutel en een eind punt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
* De antwoord uitvoer, die bestaat uit gekoppelde entiteiten (inclusief betrouwbaarheids scores, verschuivingen en webkoppelingen, voor elke document-ID), kan worden gebruikt in elke toepassing

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [De Text Analytics-client bibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Nieuwe functies](../whats-new.md)
