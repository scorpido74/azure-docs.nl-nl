---
title: Aangepaste suggesties voor automatisch voorstellen definiëren - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Aangepaste Autosuggest retourneert een lijst met voorgestelde querytekenreeksen die relevant zijn voor uw zoekervaring.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072805"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Uw aangepaste autosuggest-ervaring configureren

Aangepaste Autosuggest retourneert een lijst met voorgestelde querytekenreeksen die relevant zijn voor uw zoekervaring. De voorgestelde querytekenreeksen zijn gebaseerd op een gedeeltelijke querytekenreeks die de gebruiker in het zoekvak opgeeft. De lijst bevat maximaal 10 suggesties. 

U geeft op of u alleen aangepaste suggesties wilt retourneren of dat u ook Bing-suggesties wilt opnemen. Als u Bing-suggesties opneemt, worden aangepaste suggesties weergegeven vóór de Bing-suggesties. Als u voldoende relevante suggesties verstrekt, is het mogelijk dat de geretourneerde lijst met suggesties geen Bing-suggesties bevat. Bing-suggesties bevinden zich altijd in de context van uw aangepaste zoekinstantie. 

Als u suggesties voor zoekopdrachten voor uw instantie wilt configureren, klikt u op het tabblad **Automatisch voorstellen.**  

> [!NOTE]
> Als u deze functie wilt gebruiken, moet u zich abonneren op Aangepast zoeken op het juiste niveau (zie [prijzen).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Het kan tot 24 uur duren voordat suggesties worden weergegeven in het servereindpunt (API of gehoste gebruikersinterface).

## <a name="enable-bing-suggestions"></a>Bing-suggesties inschakelen

Als u Bing-suggesties wilt inschakelen, schakelt u de schuifregelaar **Automatische Bing-suggesties** in op de op-positie. De schuifregelaar wordt blauw.

## <a name="add-your-own-suggestions"></a>Voeg uw eigen suggesties toe

Als u uw eigen suggesties voor querytekenreeksen wilt toevoegen, voegt u deze toe aan de lijst onder door **de gebruiker gedefinieerde suggesties**. Nadat u een suggestie in de lijst hebt **+** toegevoegd, drukt u op de enter-toets of klikt u op het pictogram. U de suggestie in elke taal opgeven. U maximaal 5.000 querytekenreekssuggesties toevoegen.

## <a name="upload-suggestions"></a>Suggesties uploaden

Als optie u een lijst met suggesties uit een bestand uploaden. Het bestand moet één querytekenreeks per regel bevatten. Als u het bestand wilt uploaden, klikt u op het pictogram uploaden en selecteert u het bestand dat u wilt uploaden. De service haalt de suggesties uit het bestand en voegt deze toe aan de lijst.

## <a name="remove-suggestions"></a>Suggesties verwijderen

Als u een suggestie voor een querytekenreeks wilt verwijderen, klikt u op het pictogram Verwijderen naast de suggestie die u wilt verwijderen.

## <a name="block-suggestions"></a>Suggesties blokkeren

Als u Bing-suggesties opneemt, u een lijst met querytekenreeksen toevoegen waarvan u niet wilt dat Bing terugkeert. Als u geblokkeerde querytekenreeksen wilt toevoegen, klikt u op **Geblokkeerde suggesties weergeven**. Voeg de querytekenreeks toe aan de lijst **+** en druk op de enter-toets of klik op het pictogram. U maximaal 50 geblokkeerde querytekenreeksen toevoegen.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Het kan tot 24 uur duren voordat aangepaste configuratiewijzigingen automatisch worden doorgevoerd.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Autosuggest inschakelen in gehoste gebruikersinterface

Als u querytekenreekssuggesties voor uw gehoste gebruikersinterface wilt inschakelen, klikt u op **Gehoste gebruikersinterface**. Schuif omlaag naar de sectie **Extra configuratie.** Selecteer **onder Zoeken op internet**de optie **Aan** voor Automatisch **voorstellen inschakelen**. Als u Automatisch voorstellen wilt inschakelen, moet u een indeling selecteren die een zoekvak bevat.


## <a name="calling-the-autosuggest-api"></a>De API voor Autosuggest aanroepen

Als u voorgestelde querytekenreeksen wilt krijgen `GET` met de Bing Custom Search API, stuurt u een aanvraag naar het volgende eindpunt.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

Het antwoord bevat `SearchAction` een lijst met objecten die de voorgestelde querytekenreeksen bevatten.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Elke suggestie `displayText` bevat `query` een en veld. Het `displayText` veld bevat de voorgestelde querytekenreeks die u gebruikt om de vervolgkeuzelijst van uw zoekvak in te vullen.

Als de gebruiker een voorgestelde querytekenreeks selecteert in de vervolgkeuzelijst, gebruikt u de querytekenreeks in het `query` veld wanneer u de Bing Custom Search [API](overview.md)aanroept.


## <a name="next-steps"></a>Volgende stappen

- [Aangepaste suggesties ophalen](./get-custom-suggestions.md)
- [Uw aangepaste instantie doorzoeken](./search-your-custom-view.md)
- [Aangepaste gehoste gebruikersinterface configureren en gebruiken](./hosted-ui.md)
