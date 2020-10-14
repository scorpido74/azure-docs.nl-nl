---
title: Wat is de Bing Custom Search-API?
titleSuffix: Azure Cognitive Services
description: Met de Bing Custom Search-API kunt u op maat gemaakte zoekervaringen maken voor onderwerpen die u interesseren.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: 010f2960693a06a50cd15ac3bac2fe35b6c0985f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "84434572"
---
# <a name="what-is-the-bing-custom-search-api"></a>Wat is de Bing Custom Search-API?

Met de Bing Custom Search-API kunt u op maat gemaakte zoekervaringen zonder advertenties maken voor onderwerpen die u interesseren. U kunt opgeven in welke domeinen en op welke webpagina’s met Bing moet worden gezocht. Ook kunt u specifieke inhoud vastmaken, en het niveau ervan verhogen of verlagen, om een aangepaste weergave van het web te maken en uw gebruikers te helpen om snel relevante zoekresultaten te vinden. 

## <a name="features"></a>Functies

|Functie  |Beschrijving  |
|---------|---------|
|[Aangepaste zoeksuggesties in realtime](define-custom-suggestions.md)     | Geef zoeksuggesties op die kunnen worden weergegeven als een vervolgkeuzelijst wanneer gebruikers beginnen te typen.       | 
|[Ervaringen voor aangepast afbeeldingen zoeken](get-images-from-instance.md)     | Stel gebruikers in staat om naar afbeeldingen te zoeken vanuit de domeinen en websites die zijn opgegeven in het exemplaar voor aangepaste zoekopdrachten.        |        
|[Ervaringen voor aangepast video's zoeken](get-videos-from-instance.md)     | Stel gebruikers in staat om naar video's te zoeken vanuit de domeinen en sites die zijn opgegeven in het exemplaar voor aangepaste zoekopdrachten.        |    
|[Uw aangepaste zoekopdrachtexemplaar delen](share-your-custom-search.md)     | Bewerk en test samen uw exemplaar voor zoekopdrachten door het te delen met leden van uw team.        | 
|[Een gebruikersinterface configureren voor uw toepassingen en websites](hosted-ui.md)     | Voorziet in een gehoste gebruikersinterface die u eenvoudig in uw webpagina's en webtoepassingen als een JavaScript-codefragment kunt integreren.        | 
## <a name="workflow"></a>Werkstroom

U kunt een exemplaar voor aangepaste zoekopdrachten maken met behulp van de [portal voor Bing Custom Search](https://customsearch.ai). In de portal kunt u een exemplaar voor aangepaste zoekopdrachten maken waarin u de domeinen, websites en webpagina's opgeeft waarin Bing moet zoeken, samen met die waarin juist niet moet worden gezocht. U kunt de portal ook gebruiken voor het volgende: voorbeeld van de zoekervaring weergeven, de zoekbeoordelingen aanpassen die met de API worden geleverd, en optioneel een doorzoekbare gebruikersinterface configureren om weer te geven op websites en in toepassingen.

Nadat u uw exemplaar voor zoekopdrachten hebt gemaakt, kunt u dit (en optioneel ook een gebruikersinterface) integreren op uw website of in uw toepassing door de Bing Custom Search-API aan te roepen:

![Afbeelding die laat zien dat u via de API verbinding kunt maken met Bing Custom Search](media/BCS-Overview.png "Werking van Bing Custom Search.")


## <a name="next-steps"></a>Volgende stappen

Zie [Uw eerste Bing Custom Search-exemplaar maken](quick-start.md) om snel aan de slag te gaan.

Zie [Een exemplaar voor aangepast zoeken definiëren](define-your-custom-view.md) voor informatie over het aanpassen van uw zoekexemplaar.

Lees eerst [Vereisten voor gebruik en weergave in Bing](./use-and-display-requirements.md) voor het gebruik van zoekresultaten in uw services en toepassingen.

Bezoek de [hubpagina voor de Bing Search-API](../bing-web-search/search-the-web.md) om de andere beschikbare API's te verkennen.

Lees u in in de referentie-inhoud van alle aangepaste zoekeindpunten. De handleiding bevat de eindpunten, headers en queryparameters die u nodig hebt om zoekresultaten op te vragen. Daarnaast vindt u hier definities van de responsobjecten.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Aangepaste Video-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest-API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

