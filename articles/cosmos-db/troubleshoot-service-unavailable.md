---
title: Problemen met uitzonde ringen Azure Cosmos DB service niet beschikbaar oplossen
description: Uitzonde ringen voor Cosmos DB Service diagnosticeren en oplossen
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 12ecec7cf8f406ed53fb5e054fc304bf672cbbb0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294104"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Problemen vaststellen en oplossen Azure Cosmos DB service niet beschikbaar is
De SDK kan geen verbinding maken met de Azure Cosmos DB-service.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
De volgende lijst bevat bekende oorzaken en oplossingen voor niet-beschik bare uitzonde ringen voor services.

### <a name="1-the-required-ports-are-not-enabled"></a>1. de vereiste poorten zijn niet ingeschakeld.
Controleer of alle [vereiste poorten](performance-tips-dotnet-sdk-v3-sql.md#networking) zijn ingeschakeld.

## <a name="if-an-existing-application-or-service-started-getting-503"></a>Als een bestaande toepassing of service is begonnen met het verkrijgen van 503

### <a name="1-there-is-an-outage"></a>1. er is een storing
Controleer de [status van Azure](https://status.azure.com/status) om te zien of er sprake is van een doorlopend probleem.

### <a name="2-snat-port-exhaustion"></a>2. SNAT-poort uitputting
Volg de [SNAT-hand leiding voor poort uitputting](troubleshoot-dot-net-sdk.md#snat).

### <a name="3-the-required-ports-are-being-blocked"></a>3. de vereiste poorten worden geblokkeerd
Controleer of alle [vereiste poorten](performance-tips-dotnet-sdk-v3-sql.md#networking) zijn ingeschakeld.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) bij het gebruik van Azure Cosmos db .NET SDK
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md)