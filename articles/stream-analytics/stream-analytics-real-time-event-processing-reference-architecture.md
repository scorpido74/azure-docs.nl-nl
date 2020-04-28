---
title: Verwerking van real-time gebeurtenis met Azure Stream Analytics
description: In dit artikel wordt de referentie architectuur beschreven voor het uitvoeren van real-time gebeurtenis verwerking en-analyse met behulp van Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75431485"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Referentie architectuur: verwerking van realtime-gebeurtenissen met Microsoft Azure Stream Analytics
De referentie architectuur voor de verwerking van realtime-gebeurtenissen met Azure Stream Analytics is bedoeld om een algemene blauw druk te bieden voor het implementeren van een realtime platform as a Service (PaaS)-stroom verwerkings oplossing met Microsoft Azure.

## <a name="summary"></a>Samenvatting
De meeste analyse oplossingen zijn gebaseerd op mogelijkheden zoals ETL (extract, trans formatie, Load) en data warehousing, waarbij gegevens worden opgeslagen voordat ze worden geanalyseerd. Door de vereisten te wijzigen, met inbegrip van meer snel aankomende gegevens, moet u dit bestaande model pushen naar de limiet. De mogelijkheid voor het analyseren van gegevens in het verplaatsen van streams vóór opslag is één oplossing, en hoewel het geen nieuwe mogelijkheid is, is de aanpak niet algemeen aangenomen over alle wereld wijde verticale sectoren. 

Microsoft Azure biedt een uitgebreide catalogus met analyse technologieën die ondersteuning bieden voor een reeks verschillende scenario's en vereisten voor oplossingen. Het selecteren van de Azure-Services die voor een end-to-end-oplossing moeten worden geïmplementeerd, kan een uitdaging zijn voor de reik wijdte van de aanbiedingen. Dit document is ontworpen om de mogelijkheden en interbewerking te beschrijven van de verschillende Azure-Services die ondersteuning bieden voor een oplossing voor gebeurtenis streaming. Daarnaast worden enkele scenario's beschreven waarin klanten kunnen profiteren van dit type benadering.

## <a name="contents"></a>Inhoud
* Samenvatting
* Inleiding tot real-time analyse
* Toegevoegde waarde van realtime gegevens in azure
* Algemene Scenario's voor real-time analyse
* Architectuur en onderdelen
  * Gegevensbronnen
  * Gegevens integratie-laag
  * Realtime analyse-laag
  * Gegevensopslag laag
  * Laag van presentatie/verbruik
* Conclusie

**Auteur:** Charles Feddersen, Solution architect, data Insights Center of uitmuntendheid, micro soft Corporation

**Gepubliceerd:** Januari 2015

**Revisie:** 1,0

**Downloaden:** [verwerking van Real-Time gebeurtenis met Microsoft Azure stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Help opvragen
Probeer het [Azure stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics) voor meer hulp.

## <a name="next-steps"></a>Volgende stappen
* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

