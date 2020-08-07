---
title: Problemen met uitzonde ringen Azure Cosmos DB service niet beschikbaar oplossen
description: Uitzonde ringen voor Cosmos DB Service diagnosticeren en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987372"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Problemen vaststellen en oplossen Azure Cosmos DB service niet beschikbaar is
De SDK kan geen verbinding maken met de Azure Cosmos DB-service.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor niet-beschik bare uitzonde ringen voor services.

### <a name="1-the-required-ports-are-being-blocked"></a>1. de vereiste poorten worden geblokkeerd
Controleer of alle [vereiste poorten](performance-tips-dotnet-sdk-v3-sql.md#networking) zijn ingeschakeld.

### <a name="2-client-side-transient-connectivity-issues"></a>2. tijdelijke verbindings problemen aan de client zijde
De service niet-beschik bare uitzonde ringen kan Opper vlak zijn wanneer er tijdelijke verbindings problemen zijn die time-outs veroorzaken. Doorgaans bevat de stack tracering met betrekking tot dit scenario een `TransportException` , bijvoorbeeld:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Volg de [probleem oplossing](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) voor het oplossen van de aanvraag om dit op te lossen.

### <a name="3-service-outage"></a>3. service uitval
Controleer de [status van Azure](https://status.azure.com/status) om te zien of er sprake is van een doorlopend probleem.


## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)