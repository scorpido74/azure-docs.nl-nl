---
title: Gemiddelde tekst met behulp van de API voor tekst toezicht-Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Test: tekst toezicht testen met behulp van de tekst toezicht-API in de online-console.'
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 7a7085eccf05fb65bf7b9036c738a1d17e91a433
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935900"
---
# <a name="moderate-text-from-the-api-console"></a>Tekst met gemiddelde van de API-console

Gebruik de [tekst toezicht-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) in azure content moderator om de tekst inhoud te scannen voor scheld woorden en deze te vergelijken met aangepaste en gedeelde lijsten.

## <a name="get-your-api-key"></a>Uw API-sleutel ophalen

Voordat u de API in de online console kunt testen, moet u uw abonnements sleutel hebben. Dit bevindt zich op het tabblad **instellingen** in het vak **OCP-APIM-Subscription-Key** . Zie [Overzicht](overview.md) voor meer informatie.

## <a name="navigate-to-the-api-reference"></a>Ga naar de API-verwijzing

Ga naar de [tekst toezicht API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  De pagina **tekst scherm** wordt geopend.

## <a name="open-the-api-console"></a>Open de API-console

Voor **open API-test console**selecteert u de regio die uw locatie het meest beschrijft. 

  ![Pagina regio tekst scherm selecteren](images/test-drive-region.png)

  De API-console voor **tekst schermen** wordt geopend.

## <a name="select-the-inputs"></a>De invoer selecteren

### <a name="parameters"></a>Parameters

Selecteer de query parameters die u wilt gebruiken in het tekst scherm. Voor dit voor beeld gebruikt u de standaard waarde voor **taal**. U kunt deze waarde ook leeg laten, omdat de waarschijnlijke taal door de bewerking automatisch wordt gedetecteerd als onderdeel van de uitvoering.

> [!NOTE]
> Wijs `eng` toe of laat de para meter leeg om de **door de computer** ondersteunde **classificatie** -informatie te zien (preview-functie). **Deze functie ondersteunt alleen Engels**.
>
> Gebruik de [ISO 639-3-code](http://www-01.sil.org/iso639-3/codes.asp) **van de** ondersteunde talen die in dit artikel worden vermeld, of laat het leeg.

Selecteer **waar**voor **auto correctie**, **PII**en **classificeren (preview)** . Laat het veld **ListID** leeg.

  ![Query parameters voor de console tekst scherm](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Inhouds type

Selecteer bij **inhouds type**het type inhoud dat u wilt scherm. Voor dit voor beeld gebruikt u het standaard type **tekst/onbewerkte** inhoud. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

### <a name="sample-text-to-scan"></a>Te scannen voorbeeld tekst

Voer in het vak hoofd tekst van de aanvraag een gedeelte van de **inhoud** in. In het volgende voor beeld ziet u een opzettelijke intypen in de tekst.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Het antwoord analyseren

Het volgende antwoord toont de verschillende inzichten van de API. Het bevat mogelijk ongepaste, persoonlijke gegevens, classificatie (preview) en de automatisch gecorrigeerde versie.

> [!NOTE]
> De functie ' classificatie ' die door de machine wordt ondersteund, is beschikbaar in de preview-versie en ondersteunt alleen Engels.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
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
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
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

Raadpleeg de conceptuele hand leiding voor [tekst toezicht](text-moderation-api.md) voor een gedetailleerde uitleg van alle secties in het JSON-antwoord.

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of begin met de [tekst toezicht .net Quick](text-moderation-quickstart-dotnet.md) start om te integreren met uw toepassing.
