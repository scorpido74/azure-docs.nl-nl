---
title: Eind punten en routes beheren (Api's en CLI)
titleSuffix: Azure Digital Twins
description: Zie eind punten en gebeurtenis routes instellen en beheren voor Azure Digital Apparaatdubbels-gegevens.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7365e4904bb8e1920e7d4c57c165e489f2ff302e
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540588"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Eind punten en routes beheren in azure Digital Apparaatdubbels (Api's en CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

In azure Digital Apparaatdubbels kunt u [gebeurtenis meldingen](how-to-interpret-event-data.md) naar downstream-Services of verbonden reken bronnen sturen. Dit wordt gedaan door eerst **eind punten** in te stellen die de gebeurtenissen kunnen ontvangen. U kunt vervolgens  [**gebeurtenis routes**](concepts-route-events.md) maken om op te geven welke gebeurtenissen worden gegenereerd door Azure Digital apparaatdubbels naar welke eind punten worden verzonden.

Eind punten en routes kunnen worden beheerd met de [EventRoutes-api's](how-to-use-apis-sdks.md), de [.NET-SDK (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md). Dit artikel begeleidt u bij het proces van het maken van eind punten en routes via deze mechanismen.

Ze kunnen ook worden beheerd via de [Azure Portal](https://portal.azure.com). Voor een versie van dit artikel die gebruikmaakt van de portal, Zie [*How-to: Manage endpoints and routes (Portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Vereisten

* U hebt een **Azure-account** nodig (u kunt deze [hier](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)gratis instellen)
* U hebt een **Azure Digital apparaatdubbels-exemplaar** in uw Azure-abonnement nodig. Als u nog geen exemplaar hebt, kunt u er een maken met behulp van de stappen in [*How-to: een instantie en verificatie instellen*](how-to-set-up-instance-portal.md). Laat de volgende waarden van Setup handig zijn om later in dit artikel te gebruiken:
    - Exemplaarnaam
    - Resourcegroep
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Een eind punt maken voor Azure Digital Apparaatdubbels

Dit zijn de ondersteunde typen eind punten die u voor uw exemplaar kunt maken:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Zie [*kiezen tussen Azure Messa ging Services*](../event-grid/compare-messaging-services.md)voor meer informatie over de verschillende typen eind punten.

Als u een eind punt wilt koppelen aan Azure Digital Apparaatdubbels, moet u het gebeurtenis grid-onderwerp, Event Hub of Service Bus die u voor het eind punt gebruikt, al bestaan. 

### <a name="create-an-event-grid-endpoint"></a>Een Event Grid-eind punt maken

In het volgende voor beeld ziet u hoe u een event grid-type-eind punt maakt met behulp van de Azure CLI. U kunt [Azure Cloud shell](https://shell.azure.com)gebruiken of [de CLI lokaal installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

Eerst maakt u een event grid-onderwerp. U kunt de volgende opdracht gebruiken of de stappen in meer detail weer geven door naar [de sectie *een aangepast onderwerp maken* ](../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic) van de Snelstartgids Event grid *aangepaste gebeurtenissen* te gaan.

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Voer deze opdracht uit om een lijst uit te voeren van Azure-regionamen die kunnen worden doorgegeven aan opdrachten in de Azure CLI:
> ```azurecli
> az account list-locations -o table
> ```

Zodra u het onderwerp hebt gemaakt, kunt u dit koppelen aan Azure Digital Apparaatdubbels met de volgende [Azure Digital APPARAATDUBBELS cli-opdracht](how-to-use-cli.md):

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Het onderwerp Event grid is nu beschikbaar als een eind punt in azure Digital Apparaatdubbels, onder de naam die is opgegeven met het `--endpoint-name` argument. Normaal gesp roken gebruikt u die naam als het doel van een **gebeurtenis route**, die u [later in dit artikel](#event-routes-with-apis-and-the-c-sdk) maakt met behulp van de Azure Digital apparaatdubbels Service-API.

### <a name="create-an-event-hubs-or-service-bus-endpoint"></a>Een Event Hubs-of Service Bus-eind punt maken

Het proces voor het maken van Event Hubs-of Service Bus-eind punten is vergelijkbaar met het hierboven weer gegeven Event Grid proces.

Maak eerst uw resources die u als eind punt gaat gebruiken. Wat is er vereist:
* Service Bus: _Service Bus naam ruimte_, _Service Bus onderwerp_, _autorisatie regel_
* Event Hubs: _Event hubs naam ruimte_, _Event hub_, _autorisatie regel_

Gebruik vervolgens de volgende opdrachten om de eind punten in azure Digital Apparaatdubbels te maken: 

* Service Bus-onderwerp toevoegen-eind punt (hiervoor is een vooraf gemaakte Service Bus resource vereist)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event Hubs eind punt toevoegen (hiervoor is vooraf gemaakte Event Hubs resource vereist)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="event-routes-with-apis-and-the-c-sdk"></a>Gebeurtenis routes (met Api's en de C#-SDK)

Als u gegevens daad werkelijk van Azure Digital Apparaatdubbels naar een eind punt wilt verzenden, moet u een **gebeurtenis route**definiëren. Met Azure Digital Apparaatdubbels **EventRoutes api's** kunnen ontwikkel aars de gebeurtenis stroom, in het hele systeem en op downstream-Services, interactiviteit. Lees meer over gebeurtenis routes in [*concepten: route ring van Azure Digital apparaatdubbels-gebeurtenissen*](concepts-route-events.md).

In de voor beelden in deze sectie wordt de [.net (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)gebruikt.

Voor **waarde: u**moet eind punten maken zoals eerder in dit artikel wordt beschreven voordat u kunt verdergaan om een route te maken. U kunt door gaan met het maken van een gebeurtenis route wanneer de eind punten zijn ingesteld.

>[!NOTE]
>Als u onlangs uw eind punten hebt geïmplementeerd, controleert u of de implementatie is voltooid **voordat** u deze voor een nieuwe gebeurtenis route probeert te gebruiken. Als de implementatie van de route mislukt omdat de eind punten niet gereed zijn, wacht u enkele minuten en probeert u het opnieuw.
>
> Als u deze stroom bijwerkt, kunt u hiervoor het beste een account maken van 2-3 minuten wacht tijd voor de eindpunt service om de implementatie te volt ooien voordat u doorgaat met de installatie van.

### <a name="create-an-event-route"></a>Een gebeurtenis route maken

Gebeurtenis routes worden gedefinieerd met behulp van [Data plan-api's](how-to-use-apis-sdks.md#overview-data-plane-apis). 

Een route definitie kan deze elementen bevatten:
* De naam van de route die u wilt gebruiken
* De naam van het eind punt dat u wilt gebruiken
* Een filter dat definieert welke gebeurtenissen naar het eind punt worden verzonden 

Als er geen route naam is, worden er geen berichten meer doorgestuurd buiten Azure Digital Apparaatdubbels. Als er een route naam en het filter is `true` , worden alle berichten naar het eind punt doorgestuurd. Als er een route naam en een ander filter worden toegevoegd, worden berichten gefilterd op basis van het filter.

Voor één route moeten meerdere meldingen en gebeurtenis typen worden geselecteerd. 

`CreateEventRoute` is de SDK-aanroep die wordt gebruikt om een gebeurtenis route toe te voegen. Hier volgt een voor beeld van het gebruik:

```csharp
EventRoute er = new EventRoute("endpointName");
er.Filter("true"); //Filter allows all messages
await client.CreateEventRoute("routeName", er);
```

> [!TIP]
> Alle SDK-functies zijn beschikbaar in synchrone en asynchrone versies.

### <a name="event-route-sample-code"></a>Voorbeeld code voor gebeurtenis routering

In het volgende code voorbeeld ziet u hoe u een gebeurtenis route kunt maken, weer geven en verwijderen:
```csharp
try
{
    Console.WriteLine("Create a route: testRoute1");
    EventRoute er = new EventRoute("< your - endpoint - name >");
    // Make a filter that passes everything
    er.Filter = "true";
    client.CreateEventRoute("< your - route - name >", er);
    Console.WriteLine("Create route succeeded. Now listing routes:");
    Pageable <EventRoute> result = client.GetEventRoutes();
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Route {r.Id} to endpoint {r.EndpointId} with filter {r.Filter} ");
    }
    Console.WriteLine("Deleting routes:");
    foreach (EventRoute r in result)
    {
        Console.WriteLine($"Deleting route {r.Id}:");
        client.DeleteEventRoute(r.Id);
    }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"*** Error in event route processing ({e.ErrorCode}):\n${e.Message}");
}
```

### <a name="filter-events"></a>Gebeurtenissen filteren

Als u geen filtert, ontvangen eind punten diverse gebeurtenissen van Azure Digital Apparaatdubbels:
* Telemetrie die wordt geactiveerd door [Digital apparaatdubbels](concepts-twins-graph.md) met de Azure Digital APPARAATDUBBELS Service API
* Dubbele eigenschaps wijzigings meldingen, die worden geactiveerd bij wijzigingen in de eigenschappen voor elke dubbele in het Azure Digital Apparaatdubbels-exemplaar
* Levenscyclus gebeurtenissen, die worden geactiveerd wanneer apparaatdubbels of relaties worden gemaakt of verwijderd
* Model wijzigings gebeurtenissen, die worden geactiveerd wanneer [modellen](concepts-models.md) die zijn geconfigureerd in een Azure Digital apparaatdubbels-exemplaar worden toegevoegd of verwijderd

U kunt de verzonden gebeurtenissen beperken door een **filter** voor een eind punt toe te voegen aan de route van uw gebeurtenis.

Als u een filter wilt toevoegen, kunt u een PUT-aanvraag naar *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-preview* gebruiken met de volgende hoofd tekst:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Dit zijn de ondersteunde route filters. Gebruik de details in de kolom *filter tekst schema* om de `<filter-text>` tijdelijke aanduiding te vervangen in de hoofd tekst van de aanvraag.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

## <a name="manage-endpoints-and-routes-with-cli"></a>Eind punten en routes beheren met CLI

Eind punten en routes kunnen ook worden beheerd met behulp van de Azure Digital Apparaatdubbels CLI. Zie [*How-to: use the Azure Digital APPARAATDUBBELS cli*](how-to-use-cli.md)(Engelstalig) voor meer informatie over het gebruik van de CLI en de opdrachten die beschikbaar zijn.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende typen gebeurtenis berichten die u kunt ontvangen:
* [*Instructies: gebeurtenis gegevens interpreteren*](how-to-interpret-event-data.md)
