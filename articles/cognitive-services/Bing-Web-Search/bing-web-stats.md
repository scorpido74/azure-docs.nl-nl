---
title: Analytics toevoegen aan de Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Bing Statistics biedt analyses aan de Bing Image Search API. Analytics omvatten oproepvolume, topquerytekenreeksen, geografische distributie en meer.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 5d1b0b19523eb37aa83aa59b24114be9f76ffa55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68882781"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Analytics toevoegen aan de Bing Search API's

Bing Statistics biedt analyses voor de Bing Search API's. Deze analyses omvatten oproepvolume, topquerytekenreeksen, geografische distributie en meer. U Bing-statistieken inschakelen in de [Azure-portal](https://ms.portal.azure.com) door naar uw Azure-bron te navigeren en op **Bing-statistieken inschakelen**te klikken.

> [!IMPORTANT]
> * Bing Statistics is niet beschikbaar met gratis proefabonnementen of bronnen op de gratis `F0` prijscategorie.
> * U mag geen gegevens gebruiken die beschikbaar zijn via het Dashboard Bing Statistieken om toepassingen te maken voor distributie naar derden.
> * Als u Bing Statistics inschakelt, wordt uw abonnementstarief iets verhoogd. Zie [prijzen](https://aka.ms/bingstatisticspricing) voor meer informatie.


In de volgende afbeelding worden de beschikbare analyses voor elk Bing Search API-eindpunt weergegeven.

![Distributie per eindpuntondersteuningsmatrix](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Toegang tot uw analyses

Bing werkt elke 24 uur analysegegevens bij en houdt tot 13 maanden geschiedenis bij die u openen via het [analysedashboard.](https://bingapistatistics.com) Zorg ervoor dat u bent aangemeld met hetzelfde Microsoft-account (MSA) dat u hebt gebruikt om u aan te melden voor Bing-statistieken.

> [!NOTE]  
> * Het kan tot 24 uur duren voordat statistieken op het dashboard worden weergegeven. Het dashboard toont de datum en tijd waarop de gegevens voor het laatst zijn bijgewerkt.  
> * Statistieken zijn beschikbaar vanaf het moment dat u de invoegtoepassing Bing-statistieken inschakelt.

## <a name="filter-the-data"></a>De gegevens filteren

Standaard worden in de grafieken en grafieken alle statistieken en gegevens weergegeven waartoe u toegang hebt. U de gegevens in de grafieken en grafieken filteren door de bronnen, markten, eindpunten en rapportageperiode te selecteren waarin u geïnteresseerd bent. U de volgende filters wijzigen:

- **Resource-id:** de unieke resource-id die uw Azure-abonnement identificeert. De lijst bevat meerdere id's als u zich abonneert op meer dan één Bing Search API-laag. Standaard zijn alle resources geselecteerd.  
  
- **Markten**: De markten waar de resultaten vandaan komen. Bijvoorbeeld, en-us (Engels, Verenigde Staten). Standaard zijn alle markten geselecteerd. De `en-WW` markt is de markt die Bing gebruikt als de oproep geen markt specificeert en Bing niet in staat is om de markt van de gebruiker te bepalen.  
  
- **Eindpunten:** de eindpunten van de Bing Search API. De lijst bevat alle eindpunten waarvoor je een betaald abonnement hebt. Standaard zijn alle eindpunten geselecteerd.  

- **Tijdsbestek**: de verslagperiode. U kunt de volgende instellingen opgeven:
  - **Alles**: Bevat tot 13 maanden aan gegevens  
  - **Afgelopen 24 uur**: Inclusief analyses van de afgelopen 24 uur  
  - **Afgelopen week**: Bevat analyses van de afgelopen zeven dagen  
  - **Afgelopen maand**: Bevat analyses van de afgelopen 30 dagen  
  - **Een aangepast datumbereik:** bevat analyses uit het opgegeven datumbereik, indien beschikbaar  

## <a name="charts-and-graphs"></a>Grafieken en grafieken

Het dashboard toont grafieken en grafieken van de statistieken die beschikbaar zijn voor het geselecteerde eindpunt. Niet alle statistieken zijn beschikbaar voor alle eindpunten. De grafieken en grafieken voor elk eindpunt zijn statisch (u de grafieken en grafieken niet selecteren om weer te geven). Het dashboard toont alleen grafieken en grafieken waarvoor gegevens zijn.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Hieronder volgen mogelijke statistieken en eindpuntbeperkingen.

- **Oproepvolume:** geeft het aantal oproepen weer dat tijdens de rapportageperiode is gevoerd. Als de rapportageperiode voor een dag is, wordt in de grafiek het aantal oproepen per uur weergegeven. Anders toont de grafiek het aantal oproepen per dag van de rapportageperiode.  
  
  > [!NOTE]
  > Het gespreksvolume kan afwijken van factureringsrapporten, die over het algemeen alleen succesvolle oproepen bevatten.

- **Topquery's:** toont de hoogste query's en het aantal exemplaren van elke query tijdens de rapportageperiode. U het aantal weergegeven query's configureren. U bijvoorbeeld de top 25, 50 of 75 query's weergeven. Topquery's zijn niet beschikbaar voor de volgende eindpunten:  

  - /images/trending
  - /afbeeldingen/details
  - /images/visualsearch
  - /video's/trending
  - /video's/details
  - /nieuws
  - /nieuws/trendingtopics
  - /suggesties  
  
  > [!NOTE]  
  > Sommige querytermen kunnen worden onderdrukt om vertrouwelijke informatie te verwijderen, zoals e-mails, telefoonnummers, SSN, enz.

- **Geografische distributie**: De markten waar de zoekresultaten vandaan komen. Bijvoorbeeld (Engels, `en-us` Verenigde Staten). Bing gebruikt `mkt` de queryparameter om de markt te bepalen, indien opgegeven. Anders gebruikt Bing signalen zoals het IP-adres van de beller om de markt te bepalen.

- **Response Code Distribution:** De HTTP-statuscodes van alle oproepen tijdens de rapportageperiode.

- **Call Origin Distribution:** de typen browsers die door de gebruikers worden gebruikt. Bijvoorbeeld Microsoft Edge, Chrome, Safari en FireFox. Oproepen van buiten een browser (zoals bots, Postbode of het gebruik van krul vanuit een console-app) worden gegroepeerd onder Bibliotheken. De oorsprong wordt bepaald aan de hand van de headerwaarde user agent van de aanvraag. Als het verzoek niet de header User-Agent bevat, probeert Bing de oorsprong van andere signalen af te leiden.  

- **Safe Search Distribution**: De verdeling van veilige zoekwaarden. Bijvoorbeeld uit, matig of streng. De `safeSearch` queryparameter bevat de waarde, indien opgegeven. Anders standaard wordt de waarde standaard ingesteld als matiging.  

- **Antwoorden gevraagd distributie:** De Web Search API antwoorden `responseFilter` die u hebt aangevraagd in de query parameter.  

- **Antwoorden Geretourneerde distributie:** de antwoorden die Web Search API geretourneerd in het antwoord.

- **Response Server Distribution:** de toepassingsserver die uw API-aanvragen heeft ontvangen. De mogelijke waarden zijn Bing.com (voor verkeer dat vanaf desktop- en laptopapparaten wordt bediend) en Bing.com-mobiel (voor verkeer dat vanaf mobiele apparaten wordt bediend). De server wordt bepaald aan de hand van de headerwaarde user agent van het verzoek. Als het verzoek niet de header User-Agent bevat, probeert Bing de server af te leiden van andere signalen.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Search API's?](bing-api-comparison.md)
* [Vereisten voor gebruik en weergave van Bing Zoeken-API's](use-display-requirements.md)
