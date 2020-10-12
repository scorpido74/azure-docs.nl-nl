---
title: Fout codes voor externe Beschik baarheid-Azure Stream Analytics
description: Problemen met Azure Stream Analytics oplossen met externe fout codes voor Beschik baarheid.
ms.author: mamccrea
author: mamccrea
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: c70a3a2d0630148a077f9c149ba40d48a6b7c0ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86045277"
---
# <a name="azure-stream-analytics-external-availability-error-codes"></a>Fout codes voor externe Beschik baarheid Azure Stream Analytics

U kunt activiteiten logboeken en resource Logboeken gebruiken om fouten in het onverwachte gedrag van uw Azure Stream Analytics-taak op te lossen. In dit artikel vindt u een overzicht van de beschrijving van elke fout code voor externe Beschik baarheid. Externe beschikbaarheids fouten treden op wanneer een afhankelijke service niet beschikbaar is.

## <a name="externalserviceunavailable"></a>ExternalServiceUnavailable

* **Oorzaak**: een service is tijdelijk niet beschikbaar.
* **Aanbeveling**: de stream Analytics blijft proberen om de service te bereiken.

## <a name="eventhubmessagingerror"></a>EventHubMessagingError

* **Oorzaak**: er is een stream Analytics fout opgetreden tijdens de communicatie met EventHub. 


## <a name="next-steps"></a>Volgende stappen

* [Problemen met invoerverbindingen oplossen](stream-analytics-troubleshoot-input.md)
* [Problemen met Azure Stream Analytics uitvoer oplossen](stream-analytics-troubleshoot-output.md)
* [Problemen met Azure Stream Analytics query's oplossen](stream-analytics-troubleshoot-query.md)
* [Problemen met Azure Stream Analytics oplossen met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md)
* [Azure Stream Analytics gegevens fouten](data-errors.md)
