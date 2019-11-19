---
title: Veelgestelde vragen over Azure SignalR Service
description: Snelle toegang tot veelgestelde vragen over de Azure signalerings service, informatie over probleem oplossing en typische gebruiks scenario's.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9a8c3fd6c591f25aadc696c3c71f8c4cdfca745f
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2019
ms.locfileid: "74157566"
---
# <a name="azure-signalr-service-faq"></a>Veelgestelde vragen over Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Is Azure SignalR Service gereed voor gebruik in productieomgevingen?

Ja.
Zie [Azure SignalR Service nu algemeen beschikbaar](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) voor onze aankondiging van algemene beschikbaarheid. 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) wordt volledig ondersteund.

Ondersteuning voor ASP.NET SignalR is nog in *openbare preview*. Hier volgt een [codevoorbeeld](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>De clientverbinding wordt gesloten met het foutbericht 'Er is geen server beschikbaar'. Wat betekent dit?

Deze fout treedt alleen op wanneer er vanuit clients berichten naar de SignalR Service worden verzonden.

Als u geen toepassingsserver hebt en alleen de SignalR Service REST API gebruikt, is dit gedrag **standaard**.
In een serverloze architectuur bevinden clientverbindingen zich in de modus **LUISTEREN** en worden er via deze verbindingen geen berichten naar SignalR Service verzonden.
Meer informatie over [REST API](./signalr-quickstart-rest-api.md).

Als u toepassingsservers hebt, betekent dit foutbericht dat er geen toepassingsserver is verbonden met uw SignalR Service-exemplaar.

De mogelijke oorzaken zijn als volgt:
- Er is geen toepassingsserver verbonden met SignalR Service. Controleer de toepassingsserverlogboeken voor mogelijke verbindingsfouten. Dit is sporadisch het geval bij een hogebeschikbaarheidsinstelling met meer dan één toepassingsserver.
- Er zijn verbindingsproblemen met SignalR Service-exemplaren. Dit probleem is tijdelijk en wordt automatisch hersteld.
Als het probleem langer dan een uur aanhoudt, [opent u een probleem in GitHub](https://github.com/Azure/azure-signalr/issues/new) of [maakt u een ondersteuningsaanvraag in Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Als er meerdere toepassingsservers zijn, worden clientberichten dan naar alle servers of slechts één ervan verzonden?

Er is sprake van een-op-een-toewijzing tussen client en toepassingsserver. Berichten van één client worden altijd naar dezelfde toepassingsserver verzonden.

De toewijzing tussen client en toepassingsserver blijft behouden totdat de verbinding met de client of toepassingsserver wordt verbroken.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Als een van mijn toepassingsservers offline is, hoe kan ik deze dan vinden en hoe word ik erover geïnformeerd?

In SignalR Service worden heartbeats van toepassingsservers bewaakt.
Als er binnen een opgegeven periode geen heartbeats worden ontvangen, wordt de toepassingsserver als offline beschouwd. De verbinding met alle clients die aan deze toepassingsserver zijn toegewezen, wordt verbroken.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Waarom genereert mijn aangepaste `IUserIdProvider` een uitzondering bij het schakelen van ASP.NET Core SignalR SDK naar Azure SignalR Service SDK?

De parameter `HubConnectionContext context` is verschillend in ASP.NET Core SignalR SDK en Azure SignalR Service SDK wanneer `IUserIdProvider` wordt aangeroepen.

In ASP.NET Core SignalR is `HubConnectionContext context` de context van de fysieke clientverbinding met geldige waarden voor alle eigenschappen.

In Azure SignalR Service SDK is `HubConnectionContext context` de context van de logische clientverbinding. De fysieke clientverbinding is gekoppeld aan de SignalR Service-instantie, zodat er slechts een beperkt aantal eigenschappen wordt verstrekt.

Op dit moment zijn alleen `HubConnectionContext.GetHttpContext()` en `HubConnectionContext.User` beschikbaar voor toegang.
U kunt de broncode [hier](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs) controleren.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>Kan ik de transporten die beschikbaar zijn in SignalR Service op dezelfde manier configureren als op de server met ASP.NET Core SignalR? Kan ik WebSocket-transport bijvoorbeeld uitschakelen?

Nee.

Azure SignalR Service levert standaard alle drie de transporten die ASP.NET Core SignalR ondersteunt. Dit kan niet worden geconfigureerd. SignalR Service verwerkt verbindingen en transporten voor alle clientverbindingen.

U kunt transporten op de client configureren zoals [hier](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports) wordt beschreven.
