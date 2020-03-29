---
title: Serviceonderbrekingen in Azure Stream Analytics-taken voorkomen
description: In dit artikel worden richtlijnen beschreven om uw Stream Analytics-taken weerbaar te maken.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425994"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garandeer de betrouwbaarheid van de taak van Stream Analytics tijdens service-updates

Onderdeel van een volledig beheerde service is de mogelijkheid om nieuwe servicefunctionaliteit en verbeteringen in een snel tempo te introduceren. Als gevolg hiervan kan Stream Analytics wekelijks (of vaker) een service-update implementeren. Het maakt niet uit hoeveel testen wordt gedaan is er nog steeds een risico dat een bestaande, lopende taak kan breken als gevolg van de invoering van een bug. Als u bedrijfskritieke taken uitvoert, moeten deze risico's worden vermeden. U dit risico verminderen door het **[gekoppelde regiomodel](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** van Azure te volgen. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hoe pakken azure-gekoppelde regio's deze zorg aan?

Stream Analytics garandeert dat taken in gekoppelde regio's in afzonderlijke batches worden bijgewerkt. Als gevolg hiervan is er een voldoende tijdsverschil tussen de updates om potentiële problemen te identificeren en te verhelpen.

_Met uitzondering van Centraal-India_ (waarvan de gekoppelde regio, Zuid-India, geen Stream Analytics-aanwezigheid heeft), zou de implementatie van een update voor Stream Analytics niet tegelijkertijd plaatsvinden in een reeks gekoppelde regio's. Implementaties in meerdere regio's **in dezelfde groep** kunnen **tegelijkertijd**plaatsvinden .

Het artikel over **[beschikbaarheid en gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** heeft de meest actuele informatie over welke regio's zijn gekoppeld.

Het wordt aanbevolen om identieke taken in beide gekoppelde regio's te implementeren. U moet deze taken vervolgens [controleren](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) om een melding te krijgen wanneer er iets onverwachts gebeurt. Als een van deze taken na een update van de Stream [Analytics-service](https://docs.microsoft.com/azure/stream-analytics/job-states) in een mislukte status terechtkomt, u contact opnemen met de klantenservice om de hoofdoorzaak te identificeren. U moet ook niet over eventuele downstream consumenten om de gezonde baan output.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaginformatie over de queryvan Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Verwijzing naar restAPI-beheer van Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
