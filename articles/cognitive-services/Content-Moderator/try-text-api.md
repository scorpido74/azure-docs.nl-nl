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
ms.openlocfilehash: ad365c2d4c171105d8dec89d818ef481361d1ff8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272590"
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
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>De respons analyseren

De volgende reactie toont de verschillende inzichten uit de API. Het bevat potentiële godslastering, persoonlijke gegevens, classificatie (preview) en de automatisch gecorrigeerde versie.

> [!NOTE]
> De machine-ondersteunde 'Classificatie' functie is in preview en ondersteunt alleen Engels.

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

Raadpleeg de conceptuele gids [Voor tekstmatigheid](text-moderation-api.md) voor een gedetailleerde uitleg van alle secties in de JSON-respons.

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of volg de [.NET SDK quickstart](dotnet-sdk-quickstart.md) om te integreren met uw toepassing.
