---
title: Vermijd serviceonderbrekingen in Azure Stream Analytics-taken
description: In dit artikel bevat instructies over het maken van uw Stream Analytics-taken bijwerken robuuste.
author: jseb225
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.custom: seodec18
ms.openlocfilehash: d2d21e081b274bd985c48dac91fff5203a6b5b8a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425994"
---
# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Betrouwbaarheid van de Stream Analytics-taken te garanderen tijdens de service-updates

Onderdeel van een volledig beheerde service die wordt is de mogelijkheid om nieuwe servicefunctionaliteit en verbeteringen in een hoog tempo te introduceren. Stream Analytics kan als gevolg hiervan, een service-update implementeren op basis van wekelijkse (of vaker) hebben. Ongeacht hoeveel tests worden uitgevoerd is er nog steeds een risico dat een bestaande, actieve taak is het vanwege de introductie van een bug verbroken mogelijk. Als u kritieke taken uitvoert, moeten deze Risico's worden vermeden. U kunt dit risico verminderen door het **[gekoppelde regionale](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** model van Azure te volgen. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>Hoe dit probleem oplossen met Azure gekoppelde regio's?

Stream Analytics-taken in gekoppelde regio's worden bijgewerkt in afzonderlijke batches wordt gegarandeerd. Als gevolg hiervan is er voldoende tijds hiaat tussen de updates om mogelijke problemen te identificeren en op te lossen.

_Met uitzondering van Centraal-India_ (waarvan de gekoppelde regio Zuid-India, heeft geen Stream Analytics aanwezigheid), de implementatie van een update voor Stream Analytics wordt niet uitgevoerd op hetzelfde moment in een set van gekoppelde regio's. Implementaties in meerdere regio's **in dezelfde groep** optreden **op hetzelfde moment**.

In het artikel over **[beschikbaarheid en gekoppelde regio's](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** is de meest actuele informatie over welke regio's zijn gekoppeld.

Het wordt aanbevolen om identieke taken te implementeren in twee gekoppelde regio's. U moet [deze taken vervolgens controleren](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-set-up-alerts#scenarios-to-monitor) om een melding te ontvangen wanneer er iets onverwachts plaatsvindt. Als een van deze taken wordt beëindigd na een [mislukte status](https://docs.microsoft.com/azure/stream-analytics/job-states) na een update van een stream Analytics-service, kunt u contact opnemen met de klant ondersteuning om de hoofd oorzaak te achterhalen. U moet ook een failover uitvoeren voor alle downstream-gebruikers naar de goede taak uitvoer.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Stream Analytics query language-referentie](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Stream Analytics management REST API-naslaginformatie](https://msdn.microsoft.com/library/azure/dn835031.aspx)
