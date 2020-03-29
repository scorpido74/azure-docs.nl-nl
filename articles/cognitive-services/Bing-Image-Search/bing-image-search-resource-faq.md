---
title: Veelgestelde vragen (FAQ) - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Vind antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Bing Image Search API.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 6841e573446103466e2719797da9e4161b70b5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68881704"
---
# <a name="frequently-asked-questions-faq-about-the-bing-image-search-api"></a>Veelgestelde vragen (FAQ) over de Bing Image Search API

Vind antwoorden op veelgestelde vragen over concepten, code en scenario's met betrekking tot de Bing Image Search API voor Microsoft Cognitive Services op Azure.

## <a name="response-headers-in-javascript"></a>Antwoordkoppen in JavaScript

De volgende kopteksten kunnen optreden in reacties van de Bing Image Search API.

| `Attribute`         | `Description` |
| ------------------- | ------------- |
| `X-MSEdge-ClientID` |De unieke id die Bing aan de gebruiker heeft toegewezen |
| `BingAPIs-Market`   |De markt die werd gebruikt om aan het verzoek te voldoen |
| `BingAPIs-TraceId`  |De logboekvermelding op de Bing API-server voor dit verzoek (voor ondersteuning) |

Het is vooral belangrijk om de client-ID voort te zetten en deze terug te sturen met volgende verzoeken. Wanneer u dit doet, gebruikt de zoekopdracht eerdere context in het rangschikken van zoekresultaten en biedt het ook een consistente gebruikerservaring.

Wanneer u echter de Bing Image Search API van JavaScript aanroept, kunnen de ingebouwde beveiligingsfuncties (CORS) van uw browser ervoor zorgen dat u geen toegang krijgt tot de waarden van deze kopteksten.

Als u toegang wilt krijgen tot de kopteksten, u de Bing Image Search API-aanvraag indienen via een CORS-proxy. Het antwoord van een dergelijke proxy heeft een `Access-Control-Expose-Headers`-header waardoor antwoordheaders worden opgenomen in de whitelist en beschikbaar gemaakt voor JavaScript.

Het is eenvoudig om een CORS-proxy te installeren, zodat onze [zelfstudie-app](tutorial-bing-image-search-single-page-app.md) toegang heeft tot de optionele clientheaders. Als u [Node.js](https://nodejs.org/en/download/) nog niet hebt, moet u dit eerst installeren. Voer vervolgens de volgende opdracht in een opdrachtprompt in.

    npm install -g cors-proxy-server

Wijzig vervolgens het API-eindpunt van Bing Image Search in het HTML-bestand in:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Start ten slotte de CORS-proxy met de volgende opdracht:

    cors-proxy-server

Laat het opdrachtvenster geopend terwijl u de zelfstudie-app gebruikt. Als u het venster sluit, wordt de proxy gestopt. In de uitbreidbare sectie met HTTP-headers onder de zoekresultaten ziet u nu (onder andere) de `X-MSEdge-ClientID`-header en kunt u controleren of deze voor elke aanvraag gelijk is.

## <a name="response-headers-in-production"></a>Reactiekoppen in productie

De CORS proxy benadering beschreven in het vorige antwoord is geschikt voor ontwikkeling, testen en leren.

In een productieomgeving moet u echter een serverscript hosten op hetzelfde domein als de webpagina die de Bing Web Search API gebruikt. Dit script moet eigenlijk doen de API aanroepen op verzoek van de webpagina JavaScript en alle resultaten, met inbegrip van headers, terug naar de client. Aangezien de twee bronnen (pagina en script) een oorsprong delen, komt CORS niet in het spel en zijn de speciale headers toegankelijk voor het JavaScript op de webpagina.

Deze aanpak beschermt uw API-sleutel ook tegen blootstelling aan het publiek, omdat alleen het script aan de serverzijde het nodig heeft. Het script kan een andere methode (zoals de HTTP-verwijzer) gebruiken om ervoor te zorgen dat de aanvraag is geautoriseerd.

## <a name="next-steps"></a>Volgende stappen

Is uw vraag over een ontbrekende functie of functionaliteit? Overweeg om het aan te vragen of te stemmen op onze [User Voice-website.](https://cognitive.uservoice.com/forums/555907-bing-search)

## <a name="see-also"></a>Zie ook

 [Stack Overflow: Cognitieve services](https://stackoverflow.com/questions/tagged/bing-api)
