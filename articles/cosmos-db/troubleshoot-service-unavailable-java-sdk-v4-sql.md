---
title: Problemen met uitzonde ringen voor Azure Cosmos DB service oplossen die niet beschikbaar zijn met de Java v4 SDK
description: Meer informatie over het vaststellen en oplossen van uitzonde ringen voor Azure Cosmos DB service die niet beschikbaar zijn met de Java v4 SDK.
author: kushagrathapar
ms.service: cosmos-db
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bba6465b8978b58fa3ef7be2a7575018828eabb2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93103001"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Problemen vaststellen en oplossen Azure Cosmos DB Java v4 SDK-Service niet-beschik bare uitzonde ringen
De Java v4 SDK kan geen verbinding maken met Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor niet-beschik bare uitzonde ringen voor services.

### <a name="the-required-ports-are-being-blocked"></a>De vereiste poorten worden geblokkeerd
Controleer of alle [vereiste poorten](sql-sdk-connection-modes.md#service-port-ranges) zijn ingeschakeld.

### <a name="client-side-transient-connectivity-issues"></a>Problemen met de tijdelijke verbinding aan de client zijde
Onbeschikbaare uitzonde ringen voor services kunnen zich op het Opper vlak bevinden wanneer er tijdelijke verbindings problemen zijn die time-outs veroorzaken. Normaal gesp roken bevat de stack tracering met betrekking tot dit scenario een `ServiceUnavailableException` fout met diagnostische gegevens. Bijvoorbeeld:

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

Volg de stappen voor het oplossen van de [aanvraag time-out](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps) om deze op te lossen.

### <a name="service-outage"></a>Service-onderbreking
Controleer de [status van Azure](https://status.azure.com/status) om te zien of er een doorlopend probleem is.


## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-java-sdk-v4-sql.md) wanneer u de Azure Cosmos DB Java v4 SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [Java v4 SDK](performance-tips-java-sdk-v4-sql.md).