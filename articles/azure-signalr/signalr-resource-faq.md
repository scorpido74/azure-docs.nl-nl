---
title: Veelgestelde vragen over Azure SignalR Service
description: Snelle toegang tot veelgestelde vragen over de Azure SignalR Service, informatie over probleemoplossing en typische toepassingsscenario's.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: c944ae3a5d647cc457edd20a5d3dd0489e19e286
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192288"
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
Als het probleem langer dan een uur aanhoudt, [opent u een probleem in GitHub](https://github.com/Azure/azure-signalr/issues/new) of [maakt u een ondersteuningsaanvraag in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

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

U kunt transporten op de client configureren zoals [hier](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2) wordt beschreven.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Wat is de betekenis van metrische gegevens, zoals het aantal berichten of het aantal verbindingen in Azure Portal? Welk soort aggregatie moet ik kiezen?

U vindt [hier](signalr-concept-messages-and-connections.md) de details over hoe we deze metrische gegevens berekenen.

We hebben in de blade Overzicht van Azure SignalR Service-resources al het juiste aggregatietype voor u gekozen. En als u naar de blade Metrische gegevens gaat, kunt u het aggregatietype [hier](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) als referentie nemen.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Wat is de betekenis van de servicemodus `Default`/`Serverless`/`Classic`? Hoe kan ik kiezen?

Modi:
* Voor de `Default`-modus is de hubserver **vereist**. Als er geen serververbinding beschikbaar is voor de hub, mislukken de pogingen van de client om verbinding te maken met deze hub.
* In de `Serverless`-modus is **GEEN** serververbinding toegestaan, dat wil zeggen dat alle serververbindingen worden geweigerd en alle clients actief moeten zijn in de serverloze modus.
* De `Classic`-modus is een gemengde status. Als een hub een serververbinding heeft, wordt de nieuwe client doorgestuurd naar de hub-server. Als dat niet het geval is, zal de client de serverloze modus activeren.

  Dit kan een probleem veroorzaken. Stel dat alle serververbindingen korte tijd worden verbroken: dan zullen sommige clients de serverloze modus zullen gebruiken in plaats van door te verwijzen naar de hub-server.

Kiezen:
1. Geen hub-server, kies `Serverless`.
1. Alle hubs hebben hub-servers. Kies `Default`.
1. Sommige hubs hebben een hub-server, andere niet. Kies `Classic`. Dit kan echter een probleem veroorzaken. Het is beter om twee exemplaren te maken: `Serverless` en `Default`.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Zijn er verschillen in functies als Azure SignalR voor ASP.NET SignalR wordt gebruikt?
Als u Azure SignalR gebruikt, worden sommige API's en functies van ASP.NET SignalR niet meer ondersteund:
- De mogelijkheid om een willekeurige status door te geven tussen clients en de hub (vaak aangeduid als `HubState`) wordt niet ondersteund als Azure SignalR wordt gebruikt
- De klasse `PersistentConnection` wordt nog niet ondersteund als Azure SignalR wordt gebruikt
- **Forever Frame Transport** wordt niet ondersteund als Azure SignalR wordt gebruikt
- Azure SignalR speelt berichten die naar de client zijn verzonden niet langer af als de client offline is
- Als u Azure SignalR gebruikt, wordt het verkeer voor één clientverbinding altijd gerouteerd (ook wel **sticky**) naar één exemplaar van de app-server zolang de verbinding bestaat

De ondersteuning voor ASP.NET SignalR is gericht op compatibiliteit. Daarom worden niet alle nieuwe functies van ASP.NET Core SignalR ondersteund. **MessagePack**, **Streaming**, enzovoort, zijn bijvoorbeeld alleen beschikbaar voor ASP.NET Core SignalR-toepassingen.

SignalR Service kan worden geconfigureerd voor verschillende servicemodi: `Classic`/`Default`/`Serverles`s. In deze ondersteuning voor ASP.NET wordt de `Serverless`-modus niet ondersteund. Ook de REST API voor gegevenslagen wordt niet ondersteund.

## <a name="where-do-my-data-reside"></a>Waar worden mijn gegevens opgeslagen?

Azure SignalR Service werkt als een gegevensverwerkingsservice. Er wordt standaard geen inhoud van klanten opgeslagen. Ook staat de gegevenslocatie vast. Als u Azure SignalR Service gebruikt in combinatie met andere Azure-services, zoals Azure Storage voor diagnostische gegevens, vindt u [hier](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) hulp bij het behoud van gegevenslocaties in Azure-regio's.
