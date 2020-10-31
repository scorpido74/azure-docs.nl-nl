---
title: Problemen oplossen met uitzonde ringen Azure Cosmos DB service niet beschikbaar
description: Meer informatie over het opsporen en oplossen van uitzonde ringen voor Azure Cosmos DB service niet beschikbaar.
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 44dcaa270e167ada22d48d1061c3eb3ed3ac88f2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097869"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Problemen vaststellen en oplossen Azure Cosmos DB service niet-beschik bare uitzonde ringen
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

De SDK kan geen verbinding maken met Azure Cosmos DB.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor niet-beschik bare uitzonde ringen voor services.

### <a name="the-required-ports-are-being-blocked"></a>De vereiste poorten worden geblokkeerd
Controleer of alle [vereiste poorten](sql-sdk-connection-modes.md#service-port-ranges) zijn ingeschakeld.

### <a name="client-side-transient-connectivity-issues"></a>Problemen met de tijdelijke verbinding aan de client zijde
Onbeschikbaare uitzonde ringen voor services kunnen zich op het Opper vlak bevinden wanneer er tijdelijke verbindings problemen zijn die time-outs veroorzaken. Normaal gesp roken bevat de stack tracering die betrekking heeft op dit scenario, een `TransportException` fout. Bijvoorbeeld:

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

Volg de stappen voor het oplossen van de [aanvraag time-out](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps) om deze op te lossen.

### <a name="service-outage"></a>Service-onderbreking
Controleer de [status van Azure](https://status.azure.com/status) om te zien of er een doorlopend probleem is.


## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).