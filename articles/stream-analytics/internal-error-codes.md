---
title: Problemen oplossen met Azure Stream Analytics fout codes
description: Problemen met Azure Stream Analytics oplossen met interne fout codes.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c8c792d548fc953cb5d059f322ad1f3139c91562
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597735"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Azure Stream Analytics interne fout codes

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving voor elke interne fout code. Interne fouten zijn algemene fouten die worden gegenereerd binnen het Stream Analytics-platform wanneer Stream Analytics geen onderscheid kan maken als de fout een interne beschikbaarheids fout of een bug in het systeem is.

## <a name="kafkainvalidrequest"></a>KafkaInvalidRequest

* **Oorzaak**: de aanvraag die naar de Kafka-server wordt verzonden, is ongeldig.

## <a name="kafkainputerror"></a>KafkaInputError

* **Oorzaak**: Kafka-invoer heeft een probleem aangetroffen.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Oorzaak**: de Batch grootte die wordt gebruikt om naar Cosmos DB te schrijven, is te groot. 
* **Aanbeveling**: Probeer het opnieuw met een kleinere Batch grootte.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)