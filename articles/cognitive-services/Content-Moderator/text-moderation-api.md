---
title: Tekst toezicht-Content Moderator
titleSuffix: Azure Cognitive Services
description: Tekst toezicht gebruiken voor mogelijke ongewenste tekst, persoonlijke gegevens en aangepaste lijsten met termen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 5a07f0749b59efc96b67df3ad5ed2fbf353be614
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538847"
---
# <a name="learn-text-moderation-concepts"></a>Concepten van tekst toezicht leren

Content Moderator gebruik de door de machine gesteunde tekst toezicht en de functies voor [menselijke beoordeling](Review-Tool-User-Guide/human-in-the-loop.md) voor gematigde tekst inhoud.

U kunt de inhoud blok keren, goed keuren of controleren op basis van uw beleid en drempel waarden. Gebruik it om de menselijke toezicht te verbeteren van omgevingen waar partners, werk nemers en consumenten tekst inhoud genereren. Dit zijn onder andere chat ruimten, discussie borden, chat bots uitbreiden, e-commerce catalogi en documenten. 

De service respons bevat de volgende informatie:

- Scheld woorden: op term gebaseerde overeenkomst met ingebouwde lijst met ongepaste termen in verschillende talen
- Classificatie: door de machine ondersteunde classificatie in drie categorieën
- Persoons gegevens
- Automatisch gecorrigeerde tekst
- Oorspronkelijke tekst
- Taal

## <a name="profanity"></a>Scheld woorden

Als de API in een van de [ondersteunde talen](Text-Moderation-API-Languages.md)ongepaste termen detecteert, worden deze termen opgenomen in het antwoord. Het antwoord bevat ook hun locatie (`Index`) in de oorspronkelijke tekst. De `ListId` in de volgende JSON van het voor beeld verwijst naar termen die zijn gevonden in de [lijst met aangepaste termen](try-terms-list-api.md) , indien beschikbaar.

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 0,
        "Term": "crap"
    }

> [!NOTE]
> Wijs `eng` toe of laat de para meter leeg om de **door de computer** ondersteunde **classificatie** -informatie te zien (preview-functie). **Deze functie ondersteunt alleen Engels**.
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) **van de** ondersteunde talen die in dit artikel worden vermeld, of laat het leeg.

## <a name="classification"></a>Classificatie

De **functie voor tekst classificatie** met automatische ondersteuning van content moderator ondersteunt **alleen Engels**en helpt mogelijk ongewenste inhoud te detecteren. De gemarkeerde inhoud kan worden beoordeeld als ongepast, afhankelijk van de context. Het brengt de kans op elke categorie over en kan een menselijke beoordeling aanbevelen. De functie maakt gebruik van een getraind model om mogelijke beledigende, negatieve of discriminerende taal te identificeren. Dit omvat slang, kortere woorden, aanstootgevende woorden en opzettelijk verkeerd gespelde termen voor beoordeling. 

In de volgende extractie in de JSON-analyse wordt een voorbeeld uitvoer weer gegeven:

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

- `Category1` verwijst naar mogelijke aanwezigheid van taal die in bepaalde situaties als seksueel expliciet of volwassen kan worden beschouwd.
- `Category2` verwijst naar de mogelijke aanwezigheid van taal die in bepaalde situaties als seksueel voor stel kan worden beschouwd.
- `Category3` verwijst naar mogelijke aanwezigheid van taal die in bepaalde situaties als aanstootgevend kan worden beschouwd.
- `Score` ligt tussen 0 en 1. Hoe hoger de score, hoe hoger het model is om te voors pellen dat de categorie van toepassing kan zijn. Deze functie is afhankelijk van een statistisch model in plaats van hand matig gecodeerde resultaten. We raden u aan om te testen met uw eigen inhoud om te bepalen hoe elke categorie wordt uitgelijnd op uw vereisten.
- `ReviewRecommended` is waar of onwaar, afhankelijk van de drempel waarden van de interne Score. Klanten moeten beoordelen of u deze waarde moet gebruiken of besluiten over aangepaste drempel waarden op basis van hun inhouds beleid.

## <a name="personal-data"></a>Persoons gegevens

De functie persoonlijke gegevens detecteert de mogelijke aanwezigheid van deze gegevens:

- E-mailadres
- Post adres van de Verenigde Staten
- IP-adres
- Telefoon nummer VS
- UK-telefoon nummer
- Sociaal-fiscaal nummer (SSN)

In het volgende voor beeld ziet u een voor beeld van een antwoord:

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

Stel dat de invoer tekst (de ' lzay ' en ' f0x ' opzettelijk is):

    The qu!ck brown f0x jumps over the lzay dog.

Als u voor de automatische correctie vraagt, bevat het antwoord de gecorrigeerde versie van de tekst:

    The quick brown fox jumps over the lazy dog.

## <a name="creating-and-managing-your-custom-lists-of-terms"></a>Uw aangepaste lijsten met voor waarden maken en beheren

De standaard instelling is dat de algemene lijst met termen prima werkt in de meeste gevallen. u kunt het beste een scherm maken met de termen die specifiek zijn voor uw bedrijfs behoeften. U kunt bijvoorbeeld de naam van een concurrerend merk uit berichten van gebruikers filteren.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.
>

In het volgende voor beeld ziet u de overeenkomende lijst-ID:

    "Terms": [
    {
        "Index": 118,
        "OriginalIndex": 118,
        "ListId": 231.
        "Term": "crap"
    }

De Content Moderator biedt een [term List-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) met bewerkingen voor het beheren van aangepaste termen lijsten. Begin met de [API-console van de termen lijst](try-terms-list-api.md) en gebruik de rest API code voorbeelden. Bekijk ook de [term lijsten .net Quick](term-lists-quickstart-dotnet.md) start als u bekend bent met Visual Studio en C#.

## <a name="next-steps"></a>Volgende stappen

Test de [API-console tekst toezicht](try-text-api.md) en gebruik de rest API code voorbeelden. Bekijk ook de sectie tekst toezicht van de [.NET SDK Quick](dotnet-sdk-quickstart.md) start als u bekend bent met Visual Studio en C#.
