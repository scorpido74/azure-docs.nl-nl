---
title: Veelgestelde vragen over Azure SignalR Service
description: Krijg antwoord op veelgestelde vragen over Azure SignalR Service, waaronder probleemoplossing en typische gebruiksscenario's.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489558"
---
# <a name="azure-signalr-service-faq"></a>Veelgestelde vragen over Azure SignalR Service

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Is Azure SignalR Service gereed voor gebruik in productieomgevingen?

Ja.
Zie [Azure SignalR Service nu algemeen beschikbaar](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) voor de aankondiging van algemene beschikbaarheid. 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) wordt volledig ondersteund.

Ondersteuning voor ASP.NET SignalR is nog in *openbare preview*. [Hier volgt een codevoorbeeld](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>De clientverbinding wordt gesloten met het foutbericht 'Er is geen server beschikbaar'. Wat betekent dit?

Deze fout treedt alleen op wanneer er vanuit clients berichten naar Azure SignalR Service worden verzonden.

Als u geen toepassingsserver hebt en alleen de Azure SignalR Service REST API gebruikt, is dit gedrag *standaard*.
In een serverloze architectuur bevinden clientverbindingen zich in de modus *luisteren* en worden er via deze verbindingen geen berichten naar Azure SignalR Service verzonden.
Lees [meer over de REST API](./signalr-quickstart-rest-api.md).

Als u toepassingsservers hebt, betekent dit foutbericht dat er geen toepassingsserver is verbonden met uw Azure SignalR Service-instantie.

De mogelijke oorzaken zijn als volgt:
- Er is geen toepassingsserver verbonden met Azure SignalR Service. Controleer de toepassingsserverlogboeken voor mogelijke verbindingsfouten. Dit is sporadisch het geval bij een instelling voor hoge beschikbaarheid met meer dan één toepassingsserver.
- Er zijn verbindingsproblemen met Azure SignalR Service-instanties. Dit probleem is van tijdelijke aard en de instanties worden automatisch hersteld.
Als het probleem langer dan een uur aanhoudt, [opent u een probleem in GitHub](https://github.com/Azure/azure-signalr/issues/new) of [maakt u een ondersteuningsaanvraag in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Als er meerdere toepassingsservers zijn, worden clientberichten dan naar alle servers of slechts één ervan verzonden?

Er is sprake van een één-op-één-toewijzing tussen client en een toepassingsserver. Berichten van één client worden altijd naar dezelfde toepassingsserver verzonden.

De toewijzing wordt gehandhaafd totdat de verbinding met de client of toepassingsserver wordt verbroken.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Als een van mijn toepassingsservers offline is, hoe kan ik deze dan vinden en hoe word ik erover geïnformeerd?

Azure SignalR Service controleert de heartbeats van toepassingsservers.
Als er binnen een opgegeven periode geen heartbeats worden ontvangen, wordt de toepassingsserver als offline beschouwd. De verbinding met alle clients die aan deze toepassingsserver zijn toegewezen, wordt verbroken.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Waarom genereert mijn aangepaste `IUserIdProvider` een uitzondering wanneer ik overschakel van de ASP.NET Core SignalR-SDK naar de Azure SignalR Service-SDK?

De parameter `HubConnectionContext context` is verschillend in de ASP.NET Core SignalR-SDK en de Azure SignalR Service-SDK wanneer `IUserIdProvider` wordt aangeroepen.

In ASP.NET Core SignalR is `HubConnectionContext context` de context van de fysieke clientverbinding met geldige waarden voor alle eigenschappen.

In de Azure SignalR Service-SDK is `HubConnectionContext context` de context van de logische clientverbinding. De fysieke client is verbonden met de Azure SignalR Service-instantie, zodat er slechts een beperkt aantal eigenschappen wordt geboden.

Op dit moment zijn alleen `HubConnectionContext.GetHttpContext()` en `HubConnectionContext.User` beschikbaar voor toegang.
U kunt [de broncode controleren](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>Kan ik de transporten die beschikbaar zijn in Azure SignalR Service aan serverzijde configureren met ASP.NET Core SignalR? Kan ik WebSocket-transport bijvoorbeeld uitschakelen?

Nee.

Azure SignalR Service levert standaard alle drie de transporten die ASP.NET Core SignalR ondersteunt. Dit kan niet worden geconfigureerd. Azure SignalR Service verwerkt verbindingen en transporten voor alle clientverbindingen.

U kunt transporten aan clientzijde configureren zoals wordt beschreven in [ASP.NET Core SignalR-configuratie](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Wat is de betekenis van metrische gegevens, zoals het aantal berichten of het aantal verbindingen in Azure Portal? Welk soort aggregatie moet ik kiezen?

U vindt meer informatie over de berekening van deze metrische gegevens in [Berichten en verbindingen in Azure SignalR Service](signalr-concept-messages-and-connections.md).

In het overzichtsvenster met Azure SignalR Service-resources hebben we al het juiste aggregatietype voor u gekozen. Als u naar het deelvenster met metrische gegevens gaat, kunt u het aggregatietype meenemen naar [Berichten en verbindingen in Azure SignalR Service](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) als een referentie.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Wat is de betekenis van de servicemodi `Default`, `Serverless` en `Classic`? Hoe kan ik kiezen?

Hier volgt meer informatie over de modi:
* Voor de modus `Default` is een hubserver *vereist*. In deze modus wordt het clientverkeer door Azure SignalR Service gerouteerd naar de verbonden hubserververbindingen. Er wordt door Azure SignalR Service gecontroleerd op een verbonden hubserver. Als de service geen verbonden hubserver kan vinden, worden de binnenkomende clientverbindingen geweigerd. U kunt in deze modus ook de *Beheer-API* gebruiken om de verbonden clients rechtstreeks te beheren via Azure SignalR Service.
* De modus `Serverless` staat *geen* serververbinding toe. Dat wil zeggen dat alle serververbindingen worden geweigerd. Alle clients moeten actief zijn in de serverloze modus. Clients maken verbinding met Azure SignalR Service en gebruiken meestal serverloze technologieën, zoals *Azure Functions* voor het verwerken van hublogica. [Bekijk een eenvoudig voorbeeld](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) waarin gebruik wordt gemaakt van de serverloze modus in Azure SignalR Service.
* De `Classic`-modus is een gemengde status. Wanneer een hub een serververbinding heeft, wordt het verkeer van de nieuwe client doorgestuurd naar een hubserver. Als dat niet het geval is, gaat de client over naar de serverloze modus. 

  Dit kan een probleem veroorzaken. Als alle serververbindingen bijvoorbeeld gedurende korte tijd worden verbroken, zullen sommige clients overgaan naar de serverloze modus in plaats van verkeer door te sturen naar de hubserver.

Hier volgen enkele richtlijnen voor het kiezen van een modus:
- Als er geen hubserver is, kiest u `Serverless`.
- Als alle hubs over hubservers beschikken, kiest u `Default`.
- Als sommige hubs over hubservers beschikken, maar andere niet, kunt u `Classic` kiezen, maar dit kan een probleem veroorzaken. U kunt beter twee instanties maken: waarvan de een `Serverless` is en de andere `Default`.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Zijn er verschillen tussen de functies in Azure SignalR Service en ASP.NET SignalR?
Als u Azure SignalR Service gebruikt, worden sommige API's en functies van ASP.NET SignalR niet ondersteund:
- De mogelijkheid om een willekeurige status door te geven tussen clients en de hub (vaak aangeduid als `HubState`) wordt niet ondersteund.
- De klasse `PersistentConnection` wordt niet ondersteund.
- *Forever Frame Transport* wordt niet ondersteund.
- Azure SignalR Service speelt berichten die naar de client zijn verzonden niet langer af als de client offline is.
- Wanneer u Azure SignalR Service gebruikt, wordt het verkeer voor één clientverbinding altijd doorgestuurd (ook wel *tijdelijk* genoemd) naar één appserverinstantie voor de duur van de verbinding.

Ondersteuning voor ASP.NET SignalR is gericht op compatibiliteit. Daarom worden niet alle nieuwe functies van ASP.NET Core SignalR ondersteund. *MessagePack* en *Streaming* zijn bijvoorbeeld alleen beschikbaar voor ASP.NET Core SignalR-apps.

U kunt Azure SignalR Service configureren voor verschillende servicemodi: `Classic`, `Default` en `Serverless`. De modus `Serverless` wordt niet ondersteund voor ASP.NET. Ook de REST API voor gegevenslagen wordt niet ondersteund.

## <a name="where-does-my-data-reside"></a>Waar worden mijn gegevens opgeslagen?

Azure SignalR Service werkt als een gegevensverwerkingsservice. Er wordt standaard geen inhoud van klanten opgeslagen. Ook staat de gegevenslocatie vast. Als u Azure SignalR Service gebruikt in combinatie met andere Azure-services, zoals Azure Storage voor diagnostische gegevens, raadpleegt u [dit technische document](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) voor richtlijnen over het hanteren van gegevenslocaties in Azure-regio's.
