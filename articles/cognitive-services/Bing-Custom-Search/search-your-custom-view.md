---
title: Zoeken in een aangepaste weergave - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Nadat u uw aangepaste zoekervaring hebt geconfigureerd, u deze testen vanuit de Bing Custom Search-portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983109"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Bel uw Bing Custom Search-exemplaar vanuit de portal

Nadat u uw aangepaste zoekervaring hebt geconfigureerd, u deze testen vanuit de Bing Custom [Search-portal.](https://customsearch.ai) 

![een schermafbeelding van de aangepaste zoekportal van Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Een zoekopdracht maken 

Nadat u zich hebt aangemeld bij de [portal](https://customsearch.ai)Voor aangepast zoeken in Bing, selecteert u uw zoekexemplaar en klikt u op het tabblad **Productie.** Selecteer **onder Eindpunten**een API-eindpunt (bijvoorbeeld Web-API). Uw abonnement bepaalt welke eindpunten worden weergegeven.

Als u een zoekopdracht wilt maken, voert u de parameterwaarden voor uw eindpunt in. Houd er rekening mee dat de parameters die in de portal worden weergegeven, kunnen veranderen afhankelijk van het eindpunt dat u kiest. Zie de referentie van de [Aangepaste API voor zoeken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) voor meer informatie. Als u het abonnement wilt wijzigen dat uw zoekinstantie gebruikt, voegt u de juiste abonnementssleutel toe en werkt u de juiste markt- en/of taalparameters bij.

Enkele belangrijke parameters zijn hieronder:


|Parameter  |Beschrijving  |
|---------|---------|
|Query’s uitvoeren     | De zoekterm om naar te zoeken. Alleen beschikbaar voor web-, afbeeldings-, video- en automatisch voorstellen-eindpunten |
|Aangepaste configuratie-id | De configuratie-id van de geselecteerde instantie Aangepast zoeken. Dit veld wordt alleen gelezen. |
|Markt     | De markt waar de resultaten vandaan komen. Alleen beschikbaar voor de eindpunten web, afbeelding, video en gehoste gebruikersinterface.        |
|Abonnementssleutel | De abonnementssleutel om mee te testen. U een sleutel selecteren in de vervolgkeuzelijst of er handmatig een invoeren.          |

Als u op **Aanvullende parameters klikt,** worden de volgende parameters weergegeven:  

|Parameter  |Beschrijving  |
|---------|---------|
|Veilig zoeken     | Een filter dat wordt gebruikt om webpagina's te filteren op inhoud voor volwassenen. Alleen beschikbaar voor de eindpunten web, afbeelding, video en gehoste gebruikersinterface. Bing Custom Video Search ondersteunt slechts `moderate` `strict`twee waarden: en .        |
|Taal in de gebruikersinterface    | De taal die wordt gebruikt voor tekenreeksen van de gebruikersinterface. Als u bijvoorbeeld afbeeldingen en video's inschakelt in gehoste gebruikersinterface, gebruiken de tabbladen **Afbeelding** en **video** de opgegeven taal.        |
|Count     | Het aantal zoekresultaten dat moet worden teruggegeven in het antwoord. Alleen beschikbaar voor web-, afbeeldings- en videoeindpunten.         |
|Offset    | Het aantal zoekresultaten dat moet worden overgeslagen voordat de resultaten worden retournerd. Alleen beschikbaar voor web-, afbeeldings- en videoeindpunten.        |
    
Nadat u alle vereiste opties hebt opgegeven, klikt u op **Bellen** om het JSON-antwoord in het rechterdeelvenster weer te geven. Als u het eindpunt gehoste gebruikersinterface selecteert, u de zoekervaring in het onderste deelvenster testen.

## <a name="change-your-bing-custom-search-subscription"></a>Uw Abonnement op Aangepast zoeken in Bing wijzigen

U het abonnement wijzigen dat is gekoppeld aan uw voorbeeld voor aangepast zoeken in Bing zonder een nieuw exemplaar te maken. Als u API-aanroepen wilt laten verzonden en in rekening wilt worden gebracht voor een nieuw abonnement, maakt u een nieuwe Bing Custom Search-bron in de Azure-portal. Gebruik de nieuwe abonnementssleutel in uw API-aanvragen, samen met de aangepaste configuratie-id van uw exemplaar.

## <a name="next-steps"></a>Volgende stappen

- [Bel uw aangepaste weergave met C #](./call-endpoint-csharp.md)
- [Bel uw aangepaste weergave met Java](./call-endpoint-java.md)
- [Uw aangepaste weergave bellen met NodeJs](./call-endpoint-nodejs.md)
- [Uw aangepaste weergave bellen met Python](./call-endpoint-python.md)

- [Bel uw aangepaste weergave met de C# SDK](./sdk-csharp-quick-start.md)
