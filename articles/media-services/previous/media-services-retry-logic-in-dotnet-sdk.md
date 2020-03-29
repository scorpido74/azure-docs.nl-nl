---
title: Logica opnieuw proberen in de Media Services SDK voor .NET | Microsoft Documenten
description: Het onderwerp geeft een overzicht van logica voor het opnieuw proberen in de Media Services SDK voor .NET.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 63715f668438519131eba5bfff7aa38fc73267d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61094646"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Logica opnieuw proberen in de Media Services SDK voor .NET  

Bij het werken met Microsoft Azure-services kunnen tijdelijke fouten optreden. Als er een tijdelijke fout optreedt, in de meeste gevallen, na een paar pogingen slaagt de bewerking. De Media Services SDK voor .NET implementeert de logica voor het opnieuw proberen om tijdelijke fouten te verwerken die zijn gekoppeld aan uitzonderingen en fouten die worden veroorzaakt door webaanvragen, het uitvoeren van query's, het opslaan van wijzigingen en opslagbewerkingen.  Standaard voert de Media Services SDK voor .NET vier nieuwe pogingen uit voordat de uitzondering opnieuw naar uw toepassing wordt gegooide. De code in uw toepassing moet deze uitzondering vervolgens goed afhandelen.  

 Het volgende is een korte richtlijn voor het beleid Webrequest, Storage, Query en SaveChanges:  

* Het opslagbeleid wordt gebruikt voor blobopslagbewerkingen (uploaden of downloaden van assetbestanden).  
* Het webaanvraagbeleid wordt gebruikt voor algemene webaanvragen (bijvoorbeeld voor het verkrijgen van een verificatietoken en het oplossen van het eindpunt van het gebruikerscluster).  
* Het querybeleid wordt gebruikt voor het opvragen van entiteiten van REST (bijvoorbeeld mediaContext.Assets.Where(...)).  
* Het beleid SaveChanges wordt gebruikt om iets te doen dat gegevens binnen de service wijzigt (bijvoorbeeld het maken van een entiteit die een entiteit bijwerkt en een servicefunctie voor een bewerking aanroept).  
  
  In dit onderwerp worden uitzonderingstypen en foutcodes weergegeven die worden verwerkt door de Media Services SDK voor logica voor .NET opnieuw proberen.  

## <a name="exception-types"></a>Uitzonderingstypen
In de volgende tabel worden uitzonderingen beschreven die de Media Services SDK voor .NET verwerkt of niet verwerkt voor bepaalde bewerkingen die tijdelijke fouten kunnen veroorzaken.  

| Uitzondering | Webaanvraag | Storage | Query’s uitvoeren | Wijzigingen opslaan |
| --- | --- | --- | --- | --- |
| WebException<br/>Zie de sectie [WebException-statuscodes](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) voor meer informatie. |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Zie [HTTP-foutstatuscodes voor](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)meer informatie . |Nee |Ja |Ja |Ja |
| GegevensServiceQueryException<br/> Zie [HTTP-foutstatuscodes voor](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)meer informatie . |Nee |Ja |Ja |Ja |
| DataServiceRequestException<br/> Zie [HTTP-foutstatuscodes voor](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)meer informatie . |Nee |Ja |Ja |Ja |
| DataServiceTransportException |Nee |Nee |Ja |Ja |
| Time-outUitzondering |Ja |Ja |Ja |Nee |
| SocketUitzondering |Ja |Ja |Ja |Ja |
| StorageException |Nee |Ja |Nee |Nee |
| IOException (IOException) |Nee |Ja |Nee |Nee |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a>Statuscodes webexception
In de volgende tabel ziet u welke webexception-foutcodes de logica voor opnieuw proberen is geïmplementeerd. Met de [webexceptionstatus-opsomming](https://msdn.microsoft.com/library/system.net.webexceptionstatus.aspx) worden de statuscodes gedefinieerd.  

| Status | Webaanvraag | Storage | Query’s uitvoeren | Wijzigingen opslaan |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NameResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| VerzendenMislukt |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Nee |
| Verbindinggesloten |Ja |Ja |Ja |Nee |
| KeepAliveFailure KeepAliveFailure |Ja |Ja |Ja |Nee |
| UnknownError UnknownError UnknownError UnknownError |Ja |Ja |Ja |Nee |
| OntvangenMislukt |Ja |Ja |Ja |Nee |
| Aanvraag geannuleerd |Ja |Ja |Ja |Nee |
| Time-out |Ja |Ja |Ja |Nee |
| ProtocolFout <br/>De nieuwe poging op ProtocolError wordt beheerd door de http-statuscodeverwerking. Zie [HTTP-foutstatuscodes voor](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode)meer informatie . |Ja |Ja |Ja |Ja |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a>HTTP-foutstatuscodes
Wanneer query- en savechanges-bewerkingen GegevensServiceClientException, DataServiceQueryException of DataServiceQueryException gooien, wordt de HTTP-foutstatuscode geretourneerd in de eigenschap StatusCode.  In de volgende tabel ziet u welke foutcodes de logica voor opnieuw proberen is geïmplementeerd.  

| Status | Webaanvraag | Storage | Query’s uitvoeren | Wijzigingen opslaan |
| --- | --- | --- | --- | --- |
| 401 |Nee |Ja |Nee |Nee |
| 403 |Nee |Ja<br/>Afhandeling van nieuwe pogingen met langere wachttijden. |Nee |Nee |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Nee |
| 502 |Ja |Ja |Ja |Nee |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Nee |

Als u de daadwerkelijke implementatie van de Media Services SDK voor .NET-logica voor het opnieuw proberen wilt bekijken, raadpleegt u [azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

