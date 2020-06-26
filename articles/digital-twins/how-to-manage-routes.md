---
title: Eindpunten en routes beheren
titleSuffix: Azure Digital Twins
description: Zie eind punten en gebeurtenis routes instellen en beheren voor Azure Digital Apparaatdubbels-gegevens.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 5049e875c3b8047b99e3256c7bbac60d0af822bb
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/26/2020
ms.locfileid: "85392264"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins"></a>Eind punten en routes in azure Digital Apparaatdubbels beheren

In azure Digital Apparaatdubbels kunt u [gebeurtenis meldingen](how-to-interpret-event-data.md) door sturen naar downstream-Services of verbinding maken met reken bronnen. Dit wordt gedaan door eerst **eind punten** in te stellen die de gebeurtenissen kunnen ontvangen, gevolgd door de [**gebeurtenis routes**](concepts-route-events.md) die aangeven welke gebeurtenissen worden gegenereerd door Azure Digital apparaatdubbels naar welke eind punten worden verzonden.

Ondersteunde eindpunt typen zijn:
* [Event Hub](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Zie [kiezen tussen Azure Messa ging Services](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)voor meer informatie over de verschillende eind punten.

Eind punten en routes worden beheerd met de [**EventRoutes-api's**](how-to-use-apis-sdks.md), de [.NET-SDK (C#)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)of de [Azure Digital apparaatdubbels cli](how-to-use-cli.md).

## <a name="create-an-endpoint-for-azure-digital-twins"></a>Een eind punt maken voor Azure Digital Apparaatdubbels

Als u een eind punt wilt koppelen aan Azure Digital Apparaatdubbels, moet u de Event hub, het gebeurtenis grid-onderwerp of de Service Bus die u voor het eind punt gebruikt, al bestaan. 

In het volgende voor beeld ziet u hoe u een event grid-onderwerp maakt met behulp van Azure CLI:

```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

> [!TIP]
> Voer deze opdracht uit om een lijst uit te voeren van Azure-regionamen die kunnen worden doorgegeven aan opdrachten in de Azure CLI:
> ```azurecli
> az account list-locations -o table
> ```

Zodra u het onderwerp hebt gemaakt, kunt u dit koppelen aan Azure Digital Apparaatdubbels met de volgende opdracht:

```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Hierdoor wordt het onderwerp Event grid beschikbaar in azure Digital Apparaatdubbels, onder de naam die is opgegeven met het `--endpoint-name` argument. Normaal gesp roken gebruikt u die naam als het doel van een **gebeurtenis route**, die u in de volgende sectie maakt met de Azure Digital apparaatdubbels Service-API.

Er bestaan gelijkwaardige opdrachten voor Event hub en Service Bus-eind punten:

* Service Bus-onderwerp toevoegen-eind punt (hiervoor is een vooraf gemaakte Service Bus resource vereist)
```azurecli 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

* Event hub-eind punt toevoegen (hiervoor is vooraf gemaakte Event hub-bron vereist)
```azurecli
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

## <a name="manage-event-routes-with-apis"></a>Gebeurtenis routes beheren met Api's

Als u gegevens daad werkelijk van Azure Digital Apparaatdubbels naar een eind punt wilt verzenden, moet u een gebeurtenis route definiëren. Met Azure Digital Apparaatdubbels **EventRoutes api's** kunnen ontwikkel aars de gebeurtenis stroom, in het hele systeem en op downstream-Services, interactiviteit. Lees meer over gebeurtenis routes in [concepten: route ring van Azure Digital apparaatdubbels-gebeurtenissen](concepts-route-events.md).

In de voor beelden in dit artikel wordt de C#-SDK gebruikt.

Gebeurtenis routes worden gedefinieerd met behulp van data plan-Api's. Een route definitie kan deze elementen bevatten:
* De route-ID die u wilt gebruiken
* De naam van het eind punt dat u wilt gebruiken
* Een filter dat definieert welke gebeurtenissen naar het eind punt worden verzonden 

Als er geen route-ID is, worden er geen berichten meer doorgestuurd buiten Azure Digital Apparaatdubbels. Als er een route-ID en het filter is `true` , worden alle berichten naar het eind punt doorgestuurd. Als er een route-ID en een ander filter worden toegevoegd, worden berichten gefilterd op basis van het filter.

Voor één route moeten meerdere meldingen en gebeurtenis typen worden geselecteerd. 

`CreateEventRoute`is de SDK-aanroep die wordt gebruikt om een gebeurtenis route toe te voegen. Hier volgt een voor beeld van het gebruik:

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

U kunt de verzonden gebeurtenissen beperken door een filter toe te voegen aan een eind punt.

Als u een filter wilt toevoegen, kunt u een PUT-aanvraag naar *https://{YourHost}/EventRoutes/myNewRoute? API-Version = 2020-05 -31-preview* gebruiken met de volgende hoofd tekst:

```json  
{
    "endpointName": "<endpoint-name>",
    "filter": "<filter-text>"
}
``` 

Dit zijn de ondersteunde route filters.

| Bestandsnaam | Beschrijving | Schema filteren | Ondersteunde waarden | 
| --- | --- | --- | --- |
| Type | Het [type gebeurtenis dat](./concepts-route-events.md#types-of-event-messages) door uw digitale dubbele instantie wordt doorlopend | `"filter" : "type = '<eventType>'"` | `Microsoft.DigitalTwins.Twin.Create` <br> `Microsoft.DigitalTwins.Twin.Delete` <br> `Microsoft.DigitalTwins.Twin.Update`<br>`Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br> `Microsoft.DigitalTwins.Relationship.Delete` <br> `microsoft.iot.telemetry`  |
| Bron | Naam van het Azure Digital Apparaatdubbels-exemplaar | `"filter" : "source = '<hostname>'"`|  **Voor meldingen**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net` <br> **Voor telemetrie**:`<yourDigitalTwinInstance>.<yourRegion>.azuredigitaltwins.net/digitaltwins/<twinId>`|
| Onderwerp | Een beschrijving van de gebeurtenis in de context van de bovenstaande gebeurtenis bron | `"filter": " subject = '<subject>'"` | **Voor meldingen**: het onderwerp is`<twinid>` <br> of een URI-indeling voor onderwerpen, die uniek worden geïdentificeerd door meerdere onderdelen of Id's:<br>`<twinid>/relationships/<relationshipid>`<br> **Voor telemetrie**: het onderwerp is het pad van het onderdeel (als de telemetrie wordt verzonden vanuit een twee ledig onderdeel), zoals `comp1.comp2` . Als de telemetrie niet vanuit een onderdeel wordt verzonden, is het veld onderwerp leeg. |
| Gegevens schema | DTDL-model-ID | `"filter": "dataschema = 'dtmi:example:com:floor4;2'"` | **Voor telemetrie**: het gegevens schema is de model-id van de dubbele of de component die de telemetrie verzendt <br>**Voor meldingen**: gegevens schema wordt niet ondersteund|
| Type inhoud | Inhouds type van gegevens waarde | `"filter": "datacontenttype = '<contentType>'"` | `application/json` |
| Specificatie versie | De versie van het gebeurtenis schema dat u gebruikt | `"filter": "specversion = '<version>'"` | Moet zijn `1.0` . Dit geeft de CloudEvents-schema versie 1,0 |
| Waar/onwaar | Hiermee kunt u een route maken zonder te filteren, of een route uitschakelen | `"filter" : "<true/false>"` | `true`= route is ingeschakeld zonder filtering <br> `false`= route is uitgeschakeld |
<!--
| ID | *Not implemented for public preview* | "filter": "id = '…'" | |
| Schema | *Not implemented for public preview*  | "filter": "dataschema = '…'" | |
-->

Het is ook mogelijk om filters te combi neren met behulp van de volgende bewerkingen:

| Bestandsnaam | Schema filteren | Voorbeeld | 
| --- | --- | --- |
| EN/OF | `"filter": "<filter1> AND <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' AND datacontenttype = 'application/json'"` |
| EN/OF | `"filter": "<filter1> OR <filter2>"` | `"filter": "type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json'"` |
| Geneste bewerkingen | `"filter": "(<Comparison1>) AND (<Comparison2>)"` | `"filter": "(type != 'microsoft.iot.telemetry' OR datacontenttype = 'application/json') OR (specversion != '1.0')"` |

> [!NOTE]
> Tijdens de preview-periode wordt alleen gelijkheid van teken reeksen ondersteund (=,! =).

Wanneer u een filter implementeert of bijwerkt, kan het enkele minuten duren voordat de wijziging in de gegevens pijplijn wordt weer gegeven.

## <a name="manage-endpoints-and-routes-with-cli"></a>Eind punten en routes beheren met CLI

Eind punten en routes kunnen ook worden beheerd met behulp van de Azure Digital Apparaatdubbels CLI. De opdrachten zijn te vinden in [How to: gebruik de Azure Digital APPARAATDUBBELS cli](how-to-use-cli.md).

## <a name="monitor-event-routes"></a>Gebeurtenis routes bewaken

Routerings metrieken, zoals aantal, latentie en fout frequentie, kunnen worden weer gegeven in de [Azure Portal](https://portal.azure.com/). 

Zoek op de start pagina van de portal naar uw Azure Digital Apparaatdubbels-exemplaar om de details ervan op te halen. Selecteer de optie **metrische gegevens** in het menu van het Azure Digital apparaatdubbels-exemplaar om de pagina *metrische gegevens* weer te geven.

:::image type="content" source="media/how-to-manage-routes/metrics.png" alt-text="De pagina metrische gegevens van een Azure Digital Apparaatdubbels-instantie in de Azure Portal":::

Hier kunt u de metrische gegevens voor uw exemplaar bekijken en aangepaste weer gaven maken.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de verschillende typen gebeurtenis berichten die u kunt ontvangen:
* [Instructies: gebeurtenis gegevens interpreteren](how-to-interpret-event-data.md)
