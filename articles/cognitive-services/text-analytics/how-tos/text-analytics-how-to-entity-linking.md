---
title: Entiteitsherkenning gebruiken met de Text Analytics API
titleSuffix: Azure Cognitive Services
description: Meer informatie over het identificeren en disambiuleren van de identiteit van een entiteit in tekst met de TEXT Analytics REST API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 02/10/2020
ms.author: aahi
ms.openlocfilehash: 243086ddaae47eba20eea6877fe6d7f8f9889290
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79203488"
---
# <a name="how-to-use-named-entity-recognition-in-text-analytics"></a>Naamsbekendheid gebruiken in Text Analytics

Met de Text Analytics API u ongestructureerde tekst gebruiken en een lijst met gedesbigbiguated entiteiten retourneren, met koppelingen naar meer informatie op het web. De API ondersteunt zowel de benoemde entiteitsherkenning (NER) als entiteitskoppeling.

### <a name="entity-linking"></a>Entiteiten koppelen

Entiteit koppelen is de mogelijkheid om de identiteit van een entiteit gevonden in tekst te identificeren en `Mars` te disambiguate (bijvoorbeeld om te bepalen of een gebeurtenis van het woord verwijst naar de planeet, of naar de Romeinse god van de oorlog). Dit proces vereist de aanwezigheid van een kennisbank in een geschikte taal, om erkende entiteiten in tekst te koppelen. Entity Linking gebruikt [Wikipedia](https://www.wikipedia.org/) als kennisbank.


### <a name="named-entity-recognition-ner"></a>Benoemde entiteitsherkenning (NER)

Named Entity Recognition (NER) is de mogelijkheid om verschillende entiteiten in tekst te identificeren en te categoriseren in vooraf gedefinieerde klassen of typen zoals: persoon, locatie, gebeurtenis, product en organisatie.  

Vanaf versie 3 kan deze functie van de Text Analytics API ook persoonlijke en gevoelige informatietypen identificeren, zoals: telefoonnummer, sofinummer, e-mailadres en bankrekeningnummer.  Het identificeren van deze entiteiten kan helpen bij het classificeren van gevoelige documenten en het redacting van persoonlijke gegevens.

## <a name="named-entity-recognition-versions-and-features"></a>Benoemde entiteitsherkenningsversies en -functies

De Text Analytics API biedt twee versies van Named Entity Recognition - v2 en v3. Versie 3 (Openbare preview) biedt meer details in de entiteiten die kunnen worden gedetecteerd en gecategoriseerd.

| Functie                                                         | NER v2 | NER v3 |
|-----------------------------------------------------------------|--------|--------|
| Methoden voor enkele aanvragen en batchaanvragen                          | X      | X      |
| Basisentiteitsherkenning in verschillende categorieën              | X      | X      |
| Uitgebreide classificatie voor erkende entiteiten                 |        | X      |
| Afzonderlijke eindpunten voor het verzenden van entiteitskoppelingen en NER-aanvragen. |        | X      |
| Modelversiebeheer                                                |        | X      |

Zie [taalondersteuning](../language-support.md#sentiment-analysis-key-phrase-extraction-and-named-entity-recognition) voor informatie.


#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

### <a name="entity-types"></a>Entiteitstypen

Named Entity Recognition v3 biedt uitgebreide detectie voor meerdere typen. Momenteel kan NER v3 de volgende categorieën entiteiten herkennen:

* Algemeen
* Persoonlijke gegevens 

Zie het artikel van de door NER [v3 ondersteunde entiteitstypen](../named-entity-types.md) voor een gedetailleerde lijst met ondersteunde entiteiten en talen.

### <a name="request-endpoints"></a>Eindpunten aanvragen

Named Entity Recognition v3 gebruikt afzonderlijke eindpunten voor NER- en entiteitskoppelingsaanvragen. Gebruik hieronder een URL-indeling op basis van uw aanvraag:

Ner
* Algemene entiteiten -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Persoonlijke gegevens -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Koppeling van entiteiten
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

### <a name="model-versioning"></a>Modelversiebeheer

[!INCLUDE [v3-model-versioning](../includes/model-versioning.md)]

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

### <a name="entity-types"></a>Entiteitstypen

> [!NOTE]
> Ner versie 2 (Named Entity Recognition) ondersteunt alleen de volgende entiteiten. NER v3 is in openbare preview, en sterk breidt het aantal en de diepte van de entiteiten erkend in tekst.   

| Type  | Subtype | Voorbeeld |
|:-----------   |:------------- |:---------|
| Person        | N.v.t.\*         | "Jeff", "Bill Gates"     |
| Locatie      | N.v.t.\*         | "Redmond, Washington", "Parijs"  |
| Organisatie  | N.v.t.\*         | "Microsoft"   |
| Aantal      | Aantal        | "6", "zes"     |
| Aantal      | Percentage    | "50%", "vijftig procent"|
| Aantal      | Ordinale       | "2e", "tweede"     |
| Aantal      | Leeftijd           | "90 dagen oud", "30 jaar oud"    |
| Aantal      | Valuta      | "$10,99"     |
| Aantal      | Dimensie     | "10 mijl", "40 cm"     |
| Aantal      | Temperatuur   | "32 graden"    |
| DateTime      | N.v.t.\*         | "4:30 uur 4 februari 2012"      |
| DateTime      | Date          | "2 mei 2017", "05/02/2017"   |
| DateTime      | Time          | "8uur", "8:00"  |
| DateTime      | DateRange     | "2 mei tot 5 mei"    |
| DateTime      | TimeRange TimeRange     | "18.00 tot 19.00 uur"     |
| DateTime      | Duur      | "1 minuut en 45 seconden"   |
| DateTime      | Instellen           | "Elke dinsdag"     |
| URL           | N.v.t.\*         | "https:\//www.bing.com"    |
| Email         | N.v.t.\*         | "support@contoso.com" |
| Amerikaans telefoonnummer  | N.v.t.\*         | (Alleen telefoonnummers in de VS) "(312) 555-0176" |
| IP-adres    | N.v.t.\*         | "10.0.0.100" |

\*Afhankelijk van de input en de geëxtraheerde entiteiten kunnen bepaalde entiteiten de `SubType`.  Alle ondersteunde entiteitstypen die worden vermeld, zijn alleen beschikbaar voor de Engelse, Chinees-vereenvoudigde, Franse, Duitse en Spaanse taal.

### <a name="request-endpoints"></a>Eindpunten aanvragen

Named Entity Recognition v2 gebruikt één eindpunt voor NER- en entiteitskoppelingen:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

## <a name="sending-a-rest-api-request"></a>Een REST API-aanvraag verzenden

### <a name="preparation"></a>Voorbereiding

U moet JSON-documenten in deze indeling hebben: ID, tekst, taal.

Elk document moet minder dan 5.120 tekens bevatten en u maximaal 1.000 items (ID's) per verzameling bevatten. De verzameling is in de hoofdtekst van de aanvraag ingediend.

### <a name="structure-the-request"></a>Structureer de aanvraag

Maak een POST-aanvraag. U [Postman](text-analytics-how-to-call-api.md) of de **API-testconsole** in de volgende koppelingen gebruiken om er snel een te structureren en te verzenden. 

> [!NOTE]
> U uw sleutel en eindpunt voor uw Text Analytics-bron vinden op de azure-portal. Ze bevinden zich op de **startpagina snel van** de resource, onder **resourcebeheer.** 

#### <a name="version-30-preview"></a>[Versie 3.0-voorbeeld](#tab/version-3)

[Verwijzing naar entiteitserkenning v3](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/EntitiesRecognitionGeneral)

Versie 3 maakt gebruik van afzonderlijke eindpunten voor NER- en entiteitskoppelingenaanvragen. Gebruik hieronder een URL-indeling op basis van uw aanvraag:

Ner
* Algemene entiteiten -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/general`

* Entiteiten voor persoonlijke gegevens -`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/recognition/pii`

Koppeling van entiteiten
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0-preview.1/entities/linking`

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

[NER-verwijzing (Named Entity Recognition) v2](https://eastus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)

Versie 2 gebruikt het volgende eindpunt voor entiteitskoppelingen en NER-aanvragen: 

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v2.1/entities`

---

Stel een aanvraagkop in om uw Text Analytics API-sleutel op te nemen. Geef in de aanvraaginstantie de JSON-documenten die u hebt opgesteld.

### <a name="example-ner-request"></a>Voorbeeld NER-aanvraag 

Het volgende is een voorbeeld van inhoud die u naar de API verzenden. De aanvraagindeling is hetzelfde voor beide versies van de API.

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

Analyse wordt uitgevoerd na ontvangst van de aanvraag. Zie de sectie [gegevenslimieten](../overview.md#data-limits) in het overzicht voor informatie over de grootte en het aantal aanvragen dat u per minuut en seconde verzenden.

De Text Analytics API is stateloos. Er worden geen gegevens opgeslagen in uw account en de resultaten worden onmiddellijk geretourneerd in het antwoord.

## <a name="view-results"></a>Resultaten weergeven

Alle POST-aanvragen retourneren een JSON-opgemaakte reactie met de id's en gedetecteerde entiteitseigenschappen.

Uitvoer wordt onmiddellijk geretourneerd. U kunt de resultaten streamen naar een toepassing die JSON accepteert of u kunt de uitvoer opslaan als lokaal bestand en vervolgens importeren in een toepassing waarmee u kunt sorteren, zoeken en de gegevens kunt manipuleren. Vanwege ondersteuning voor meertaligen en emoji's kan het antwoord tekstverschuivingen bevatten. Zie [hoe u tekstverschuivingen verwerkt](../concepts/text-offsets.md) voor meer informatie.

#### <a name="version-30-preview"></a>[Versie 3.0-preview)](#tab/version-3)

### <a name="example-v3-responses"></a>Voorbeeld v3-antwoorden

Versie 3 biedt afzonderlijke eindpunten voor NER- en entiteitskoppelingen. De antwoorden voor beide bewerkingen zijn hieronder. 

#### <a name="example-ner-response"></a>Voorbeeld NER-reactie

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

#### <a name="example-entity-linking-response"></a>Voorbeeld entiteit die respons koppelt

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

#### <a name="version-21"></a>[Versie 2.1](#tab/version-2)

### <a name="example-ner-v2-response"></a>Voorbeeld NER v2-respons
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

In dit artikel hebt u concepten en werkstroom geleerd voor entiteitskoppeling met Text Analytics in Cognitive Services. Samenvatting:

* Benoemde entiteitsherkenning is beschikbaar voor geselecteerde talen in twee versies.
* JSON-documenten in de aanvraaginstantie bevatten een ID, tekst en taalcode.
* POST-verzoeken worden verzonden naar een of meer eindpunten, met behulp van een gepersonaliseerde [toegangssleutel en een eindpunt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dat geldig is voor uw abonnement.
* Responsuitvoer, die bestaat uit gekoppelde entiteiten (inclusief betrouwbaarheidsscores, compensaties en webkoppelingen voor elke document-id) kan in elke toepassing worden gebruikt

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Text Analytics](../overview.md)
* [De tekstanalyse-clientbibliotheek gebruiken](../quickstarts/text-analytics-sdk.md)
* [Wat is er nieuw](../whats-new.md)
