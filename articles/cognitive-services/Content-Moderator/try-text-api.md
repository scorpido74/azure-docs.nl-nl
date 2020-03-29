---
title: Tekst beheren met behulp van de API voor tekstbeheer - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Tekstmoderatie voor testsstation met behulp van de API voor tekstbeheer in de online console.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74538831"
---
# <a name="moderate-text-from-the-api-console"></a>Tekst uit de API-console matigen

Gebruik de [API voor tekstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in Azure-inhoudsmoderator om uw tekstinhoud te scannen op godslastering en deze te vergelijken met aangepaste en gedeelde lijsten.

## <a name="get-your-api-key"></a>Uw API-sleutel ophalen

Voordat u de API testen in de online console, hebt u uw abonnementssleutel nodig. Dit bevindt zich op het tabblad **Instellingen** in het vak **Ocp-Apim-Subscription-Key.** Zie [Overzicht](overview.md) voor meer informatie.

## <a name="navigate-to-the-api-reference"></a>Navigeren naar de API-verwijzing

Ga naar de [API-verwijzing voor tekstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  De **pagina Tekst - Scherm** wordt geopend.

## <a name="open-the-api-console"></a>De API-console openen

Selecteer **voor open API-testconsole**het gebied dat uw locatie het meest beschrijft. 

  ![Tekst - Selectie van schermpaginaregio's](images/test-drive-region.png)

  De **API-console Tekst - Scherm** wordt geopend.

## <a name="select-the-inputs"></a>De ingangen selecteren

### <a name="parameters"></a>Parameters

Selecteer de queryparameters die u in het tekstscherm wilt gebruiken. Gebruik in dit voorbeeld de standaardwaarde voor **taal**. U het ook leeg laten omdat de bewerking automatisch de waarschijnlijke taal detecteert als onderdeel van de uitvoering ervan.

> [!NOTE]
> Wijs deze voor `eng` de **taalparameter** toe of laat deze leeg om het door de machine ondersteunde **classificatieantwoord** (voorbeeldfunctie) te zien. **Deze functie ondersteunt alleen Engels.**
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) van de ondersteunde talen in dit artikel of laat deze leeg voor detectie **van godslasteringstermen.**

Selecteer **true**voor **autocorrectie**, **PII**en **classificeren (voorbeeld).** Laat het **veld ListId** leeg.

  ![Tekst - Parameters voor schermconsolequery's](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Inhoudstype

Selecteer **bij Inhoudstype**het type inhoud dat u wilt screenen. Gebruik in dit voorbeeld het **standaardtekst-/inhoudstype.** Voer in het vak **Ocp-Apim-Subscription-Key** uw abonnementssleutel in.

### <a name="sample-text-to-scan"></a>Voorbeeldtekst om te scannen

Voer in het vak **Hoofdtekst aanvragen** tekst in. In het volgende voorbeeld ziet u een opzettelijke typefout in de tekst.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>De respons analyseren

De volgende reactie toont de verschillende inzichten uit de API. Het bevat potentiële godslastering, persoonlijke gegevens, classificatie (preview) en de automatisch gecorrigeerde versie.

> [!NOTE]
> De machine-ondersteunde 'Classificatie' functie is in preview en ondersteunt alleen Engels.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
"Misrepresentation":null,
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
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
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
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Raadpleeg de conceptuele gids [Voor tekstmatigheid](text-moderation-api.md) voor een gedetailleerde uitleg van alle secties in de JSON-respons.

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of volg de [.NET SDK quickstart](dotnet-sdk-quickstart.md) om te integreren met uw toepassing.
