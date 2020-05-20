---
title: Problemen oplossen met Azure Stream Analytics fout codes
description: Problemen met Azure Stream Analytics oplossen met interne fout codes.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: 2e101747322baac860cbde02aca341171aa0cc1d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83658342"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics interne fout codes

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving voor elke interne fout code. Interne fouten zijn algemene fouten die worden gegenereerd binnen het Stream Analytics-platform wanneer Stream Analytics geen onderscheid kan maken als de fout een interne beschikbaarheids fout of een bug in het systeem is.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Oorzaak**: de Batch grootte die wordt gebruikt om naar Cosmos DB te schrijven, is te groot. 
* **Aanbeveling**: Probeer het opnieuw met een kleinere Batch grootte.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)