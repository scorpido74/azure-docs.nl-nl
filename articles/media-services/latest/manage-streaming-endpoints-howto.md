---
title: Streaming eindpunten beheren met Azure Media Services v3
description: In dit artikel wordt uitgelegd hoe u streaming eindpunten beheert met Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461041"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Streaming eindpunten beheren met Media Services v3

Wanneer uw Media Services-account wordt gemaakt, wordt een **standaard** [streamingeindpunt](streaming-endpoint-concept.md) toegevoegd aan uw account in de status **Gestopt.** Om te beginnen met het streamen van uw inhoud en te profiteren van [dynamische verpakking](dynamic-packaging-overview.md) en [dynamische versleuteling,](content-protection-overview.md)moet het streaming eindpunt van waaruit u inhoud wilt streamen, in de **status Actief** zijn.

In dit artikel ziet u hoe u de [startopdracht](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) op uw streaming-eindpunt uitvoert met behulp van verschillende technologieën. 
 
> [!NOTE]
> Er worden alleen kosten in rekening gebracht wanneer uw streamingeindpunt in de werktoestand is.
    
## <a name="prerequisites"></a>Vereisten

Review: 

* [Concepten van Media Services](concepts-overview.md)
* [Streaming Endpoint concept](streaming-endpoint-concept.md)
* [Dynamische verpakking](dynamic-packaging-overview.md)

## <a name="use-rest"></a>REST gebruiken

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Zie voor meer informatie: 

* Een [verwijzingsdocumentatie voor StreamingEndpoint starten.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Het starten van een streaming eindpunt is een asynchrone bewerking. 

    Zie [Langlopende bewerkingen](media-services-apis-overview.md)voor informatie over het controleren van langlopende bewerkingen.
* Deze [Postman-collectie](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) bevat voorbeelden van meerdere REST-bewerkingen, waaronder over het starten van een streaming-eindpunt.

## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken 
 
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
1. Ga naar uw Azure Media Services-account.
1. Selecteer In het linkerdeelvenster de optie **Streaming-eindpunten**.
1. Selecteer het streamingeindpunt dat u wilt starten en selecteer **Vervolgens Start**.

## <a name="use-the-azure-cli"></a>Azure CLI gebruiken

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Zie voor meer informatie [de start van az ams streaming-endpoint.](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start)

## <a name="use-sdks"></a>SDK's gebruiken

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Bekijk het volledige [Java-codevoorbeeld.](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128)

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Zie het volledige [voorbeeld van de .NET-code](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Volgende stappen

* [Media Services v3 OpenAPI-specificatie (Branie)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Streaming Endpoint-bewerkingen](https://docs.microsoft.com/rest/api/media/streamingendpoints)
