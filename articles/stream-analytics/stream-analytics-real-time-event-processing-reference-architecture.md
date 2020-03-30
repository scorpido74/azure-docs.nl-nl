---
title: Realtime gebeurtenisverwerking met Azure Stream Analytics
description: In dit artikel wordt de referentiearchitectuur beschreven om realtime gebeurtenisverwerking en -analyses te bereiken met Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431485"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referentiearchitectuur: realtime gebeurtenisverwerking met Microsoft Azure Stream Analytics
De referentiearchitectuur voor realtime gebeurtenisverwerking met Azure Stream Analytics is bedoeld om een algemene blauwdruk te bieden voor het implementeren van een paas-streamverwerkingsoplossing (Real-Time- platform as a Service) met Microsoft Azure.

## <a name="summary"></a>Samenvatting
Van oudsher zijn analyseoplossingen gebaseerd op mogelijkheden zoals ETL (extract, transform, load) en data warehousing, waarbij gegevens worden opgeslagen voorafgaand aan de analyse. Veranderende eisen, waaronder sneller binnenkomende gegevens, brengen dit bestaande model tot het uiterste. De mogelijkheid om gegevens te analyseren binnen bewegende stromen voorafgaand aan opslag is een oplossing, en hoewel het geen nieuwe mogelijkheid is, is de aanpak niet op grote schaal gekozen voor alle brancheverticalen. 

Microsoft Azure biedt een uitgebreide catalogus van analysetechnologieën die een reeks verschillende oplossingsscenario's en vereisten kunnen ondersteunen. Als u selecteert welke Azure-services moeten worden geïmplementeerd voor een end-to-end-oplossing, kan dit een uitdaging zijn, gezien de breedte van het aanbod. Dit document is ontworpen om de mogelijkheden en de interoperation van de verschillende Azure-services te beschrijven die een oplossing voor het streamen van gebeurtenissen ondersteunen. Het verklaart ook een aantal scenario's waarin klanten kunnen profiteren van dit soort aanpak.

## <a name="contents"></a>Inhoud
* Samenvatting
* Inleiding tot real-time analytics
* Waardepropositie van realtime gegevens in Azure
* Veelvoorkomende scenario's voor realtime analyses
* Architectuur en componenten
  * Gegevensbronnen
  * Laag gegevens-integratie
  * Realtime analyselaag
  * Laag gegevensopslag
  * Presentatie / verbruikslaag
* Conclusie

**Auteur:** Charles Feddersen, Solution Architect, Data Insights Center of Excellence, Microsoft Corporation

**Gepubliceerd:** Januari 2015

**Revisie:** 1.0

**Downloaden:** [Realtime gebeurtenisverwerking met Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Help opvragen
Probeer het Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) voor meer hulp

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

