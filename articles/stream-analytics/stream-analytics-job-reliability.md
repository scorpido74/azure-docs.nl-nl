---
title: Voorkom onderbrekingen in de service in Azure Stream Analytics taken
description: In dit artikel wordt uitgelegd hoe u de upgrade van uw Stream Analytics-taken flexibeler maakt.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75425994"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Betrouw baarheid Stream Analytics-taak garanderen tijdens service-updates

Een deel van een volledig beheerde service is de mogelijkheid om in een snel tempo nieuwe service functionaliteit en verbeteringen te introduceren. Als gevolg hiervan kunnen Stream Analytics een service-update wekelijks (of vaker) implementeren. Hoe veel tests er worden uitgevoerd, is nog steeds een risico dat een bestaande, lopende taak kan worden verbroken vanwege de introductie van een fout. Als u kritieke taken uitvoert, moeten deze Risico's worden vermeden. U kunt dit risico verminderen door het **[gekoppelde regionale](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** model van Azure te volgen. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hoe is het adres van gekoppelde Azure-regio's het probleem?

Stream Analytics garandeert dat taken in gekoppelde regio's worden bijgewerkt in afzonderlijke batches. Als gevolg hiervan is er voldoende tijds hiaat tussen de updates om mogelijke problemen te identificeren en op te lossen.

_Met uitzonde ring van Centraal-India_ (waarvan de gekoppelde regio, India-Zuid, heeft geen stream Analytics aanwezigheid), wordt de implementatie van een update naar stream Analytics niet tegelijkertijd uitgevoerd in een set gekoppelde regio's. Implementaties in meerdere regio's **in dezelfde groep** kunnen **op hetzelfde moment**plaatsvinden.

Het artikel over **[Beschik baarheid en gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** heeft de meest actuele informatie over welke regio's worden gekoppeld.

Het wordt aanbevolen om identieke taken te implementeren in twee gekoppelde regio's. U moet [deze taken vervolgens controleren](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) om een melding te ontvangen wanneer er iets onverwachts plaatsvindt. Als een van deze taken wordt beëindigd na een [mislukte status](https://docs.microsoft.com/azure/stream-analytics/job-states) na een update van een stream Analytics-service, kunt u contact opnemen met de klant ondersteuning om de hoofd oorzaak te achterhalen. U moet ook een failover uitvoeren voor alle downstream-gebruikers naar de goede taak uitvoer.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslag informatie voor de query taal Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Naslag informatie over Stream Analytics beheer REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
