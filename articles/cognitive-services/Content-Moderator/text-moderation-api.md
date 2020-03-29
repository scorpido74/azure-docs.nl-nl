---
title: Tekstmoderatie - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Gebruik tekstmoderatie voor mogelijke ongewenste tekst, persoonlijke gegevens en aangepaste lijsten met termen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538847"
---
# <a name="learn-text-moderation-concepts"></a>Ideeën voor tekstbeheer

Gebruik de machinematige tekstmoderatie en [de](Review-Tool-User-Guide/human-in-the-loop.md) mogelijkheden voor menselijke beoordeling van contentmoderator om tekstinhoud te modereren.

U kunt de inhoud blokkeren, goedkeuren of controleren op basis van uw beleid en drempelwaarden. Gebruik het om menselijke moderatie van omgevingen waar partners, werknemers en consumenten tekstinhoud genereren, te vergroten. Het gaat hierbij om chatruimten, discussieborden, chatbots, e-commerce-catalogi en documenten. 

Het antwoord van de service bevat de volgende informatie:

- Godslastering: term-based matching met ingebouwde lijst van profane termen in verschillende talen
- Indeling: machine-ondersteunde classificatie in drie categorieën
- Persoonsgegevens
- Automatisch gecorrigeerde tekst
- Oorspronkelijke tekst
- Taal

## <a name="profanity"></a>Grof taalgebruik

Als de API profane termen in een van de [ondersteunde talen](Text-Moderation-API-Languages.md)detecteert, worden deze termen opgenomen in het antwoord. Het antwoord bevat ook`Index`hun locatie ( ) in de oorspronkelijke tekst. De `ListId` in de volgende steekproef JSON verwijst naar termen gevonden in [aangepaste termen lijsten](try-terms-list-api.md) indien beschikbaar.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Wijs deze voor `eng` de **taalparameter** toe of laat deze leeg om het door de machine ondersteunde **classificatieantwoord** (voorbeeldfunctie) te zien. **Deze functie ondersteunt alleen Engels.**
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) van de ondersteunde talen in dit artikel of laat deze leeg voor detectie **van godslasteringstermen.**

## <a name="classification"></a>Classificatie

De machineondersteunde **tekstclassificatiefunctie** van Content Moderator ondersteunt **alleen Engels**en helpt bij het detecteren van mogelijk ongewenste inhoud. De gemarkeerde inhoud kan worden beoordeeld als ongepast, afhankelijk van de context. Het geeft de waarschijnlijkheid van elke categorie en kan een menselijke beoordeling aanbevelen. De functie maakt gebruik van een getraind model om mogelijke beledigende, denigrerende of discriminerende taal te identificeren. Dit omvat slang, verkorte woorden, beledigend, en opzettelijk verkeerd gespelde woorden voor beoordeling. 

In het volgende uittreksel in het JSON-extract ziet u een voorbeelduitvoer:

    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
            },
        "Category2": {
            "Score": 0.12747249007225037
            },
        "Category3": {
            "Score": 0.98799997568130493
        }
    }

### <a name="explanation"></a>Uitleg

- `Category1`verwijst naar mogelijke aanwezigheid van taal die in bepaalde situaties als seksueel expliciet of volwassen kan worden beschouwd.
- `Category2`verwijst naar mogelijke aanwezigheid van taal die in bepaalde situaties als seksueel suggestief of volwassen kan worden beschouwd.
- `Category3`verwijst naar mogelijke aanwezigheid van taal die in bepaalde situaties als beledigend kan worden beschouwd.
- `Score`is tussen 0 en 1. Hoe hoger de score, hoe hoger het model voorspelt dat de categorie van toepassing kan zijn. Deze functie is gebaseerd op een statistisch model in plaats van handmatig gecodeerde resultaten. We raden u aan te testen met uw eigen inhoud om te bepalen hoe elke categorie aansluit bij uw vereisten.
- `ReviewRecommended`is waar of onwaar, afhankelijk van de interne scoredrempels. Klanten moeten beoordelen of ze deze waarde moeten gebruiken of aangepaste drempels moeten bepalen op basis van hun inhoudsbeleid.

## <a name="personal-data"></a>Persoonsgegevens

De functie persoonsgegevens detecteert de mogelijke aanwezigheid van deze informatie:

- E-mailadres
- Amerikaans postadres
- IP-adres
- Amerikaans telefoonnummer
- Brits telefoonnummer
- SSN (SSN)

In het volgende voorbeeld ziet u een voorbeeldreactie:

```json
"PII":{ 
  "Email":[ 
    { 
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[ 
    { 
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[ 
    { 
      "CountryCode":"US",
      "Text":"4255550111",
      "Index":56
    },
    { 
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":212
    },
    { 
      "CountryCode":"UK",
      "Text":"+123 456 7890",
      "Index":208
    },
    { 
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":228
    },
    { 
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":245
    }
  ],
  "Address":[ 
    { 
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
      "Index":89
    }
  ],
  "SSN":[ 
    { 
      "Text":"999999999",
      "Index":56
    },
    { 
      "Text":"999-99-9999",
      "Index":267
    }
  ]
}
```

## <a name="auto-correction"></a>Automatische correctie

Stel dat de invoertekst is (de 'lzay' en 'f0x' opzettelijk zijn):

    The qu!ck brown f0x jumps over the lzay dog.

Als u om automatische correctie vraagt, bevat het antwoord de gecorrigeerde versie van de tekst:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Uw aangepaste lijsten met termen maken en beheren

Hoewel de standaard, globale lijst met termen voor de meeste gevallen geweldig werkt, u screenen op termen die specifiek zijn voor uw bedrijfsbehoeften. U bijvoorbeeld concurrerende merknamen filteren uit berichten van gebruikers.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.
>

In het volgende voorbeeld wordt de overeenkomende lijst-id weergegeven:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

De inhoudsmoderator biedt een [TERM List API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) met bewerkingen voor het beheren van aangepaste termenlijsten. Begin met de [API-console termlists](try-terms-list-api.md) en gebruik de monsters van de REST API-code. Bekijk ook de [Term Lists .NET quickstart](term-lists-quickstart-dotnet.md) als u bekend bent met Visual Studio en C#.

## <a name="next-steps"></a>Volgende stappen

Test station de [Text moderation API console](try-text-api.md) en gebruik de REST API code samples. Bekijk ook de sectie Tekstmatiging van de [.NET SDK snelstart](dotnet-sdk-quickstart.md) als u bekend bent met Visual Studio en C#.
