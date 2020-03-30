---
title: Inzicht in Azure IoT Hub-berichtroutering | Microsoft Documenten
description: Handleiding voor ontwikkelaars - hoe u berichtroutering gebruiken om berichten van apparaat naar cloud te verzenden. Bevat informatie over het verzenden van zowel telemetrie- als niet-telemetriegegevens.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: d10744f2536cdf89115cdccd0bea6f1e5155774c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370454"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub-berichtroutering gebruiken om device-to-cloudberichten naar verschillende eindpunten te verzenden

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met berichtroutering u berichten van uw apparaten naar cloudservices verzenden op een geautomatiseerde, schaalbare en betrouwbare manier. Berichtroutering kan worden gebruikt voor: 

* **Het verzenden van apparaattelemetrieberichten en gebeurtenissen,** namelijk gebeurtenissen in de levenscyclus van apparaten en gebeurtenissen van apparaattwee wijzigingen in de ingebouwde eindpunten en aangepaste eindpunten. Meer informatie over [het routeren van eindpunten](#routing-endpoints).

* **Gegevens filteren voordat u deze naar verschillende eindpunten routert** door uitgebreide query's toe te passen. Met berichtroutering u de eigenschappen van het bericht en de berichttekst opvragen, evenals dubbele tags voor apparaten en dubbele eigenschappen van het apparaat. Meer informatie over het gebruik van [query's in berichtroutering](iot-hub-devguide-routing-query-syntax.md).

IoT Hub heeft schrijftoegang tot deze serviceeindpunten nodig voor het routeren van berichten naar het werk. Als u uw eindpunten configureert via de Azure-portal, worden de benodigde machtigingen voor u toegevoegd. Zorg ervoor dat u uw services configureert om de verwachte doorvoer te ondersteunen. Als u bijvoorbeeld Gebeurtenishubs als aangepast eindpunt gebruikt, moet u de **doorvoereenheden** voor die gebeurtenishub configureren, zodat het de instroom van gebeurtenissen die u via iot-hub-berichtroutering wilt verzenden, kan verwerken. Wanneer u een servicebuswachtrij als eindpunt gebruikt, moet u de **maximale grootte** configureren om ervoor te zorgen dat de wachtrij alle gegevens kan bevatten die zijn binnengevallen, totdat deze door consumenten wordt beëindigd. Wanneer u uw IoT-oplossing voor het eerst configureert, moet u mogelijk uw extra eindpunten controleren en de nodige aanpassingen aanbrengen voor de werkelijke belasting.

De IoT Hub definieert een [gemeenschappelijk formaat](iot-hub-devguide-messages-construct.md) voor alle device-to-cloud messaging voor interoperabiliteit tussen protocollen. Als een bericht overeenkomt met meerdere routes die naar hetzelfde eindpunt wijzen, levert IoT Hub slechts één keer een bericht naar dat eindpunt. Daarom hoeft u deduplicatie niet te configureren in de wachtrij of het onderwerp ServiceBus. In partitiewachtrijen garandeert partitieaffiniteit het bestellen van berichten. Gebruik deze zelfstudie om te leren hoe u [berichtroutering configureert.](tutorial-routing.md)

## <a name="routing-endpoints"></a>Routeringseindpunten

Een IoT-hub heeft een standaard ingebouwde-eindpunt **(berichten/gebeurtenissen)** dat compatibel is met gebeurtenishubs. U [aangepaste eindpunten](iot-hub-devguide-endpoints.md#custom-endpoints) maken om berichten naar te leiden door andere services in uw abonnement te koppelen aan de IoT Hub. 

Elk bericht wordt doorgestuurd naar alle eindpunten waarvan de routeringsquery's overeenkomen. Met andere woorden, een bericht kan worden doorgestuurd naar meerdere eindpunten.

IoT Hub ondersteunt momenteel de volgende services als aangepaste eindpunten:

### <a name="built-in-endpoint"></a>Ingebouwd eindpunt

U standaard [integratie van Event Hubs en SDK's](iot-hub-devguide-messages-read-builtin.md) gebruiken om device-to-cloudberichten te ontvangen van het ingebouwde eindpunt **(berichten/gebeurtenissen).** Zodra een route is gemaakt, worden de gegevens niet meer naar het ingebouwde eindpunt geleid, tenzij er een route naar dat eindpunt wordt gemaakt.

### <a name="azure-storage"></a>Azure Storage

Er zijn twee opslagservices waar IoT Hub berichten naar kan routeren: [Azure Blob Storage-](../storage/blobs/storage-blobs-introduction.md) en [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) -accounts (ADLS Gen2). Azure Data Lake Storage-accounts zijn [hiërarchische naamruimte-ingeschakelde](../storage/blobs/data-lake-storage-namespace.md)opslagaccounts die zijn gebouwd bovenop blob-opslag. Beide gebruiken blobs voor hun opslag.

IoT Hub ondersteunt het schrijven van gegevens naar Azure Storage in de [Apache Avro-indeling](https://avro.apache.org/) en in JSON-indeling. De standaard is AVRO. De coderingsindeling kan alleen worden ingesteld wanneer het eindpunt van de blobopslag is geconfigureerd. De indeling kan niet worden bewerkt voor een bestaand eindpunt. Wanneer u JSON-codering gebruikt, moet u de contentType instellen op **toepassing/json** en contentEncoding op **UTF-8** in de eigenschappen van het [berichtsysteem.](iot-hub-devguide-routing-query-syntax.md#system-properties) Beide waarden zijn case-ongevoelig. Als de inhoudscodering niet is ingesteld, schrijft IoT Hub de berichten in de basis64 gecodeerde indeling. U de coderingsindeling selecteren met de IOT Hub Create or Update REST API, met name de [RoutingStorageContainerProperties,](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties)de Azure portal, [Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)of de [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). In het volgende diagram ziet u hoe u de coderingsindeling in de Azure-portal selecteert.

![Codering van blobopslageindpunten](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub batches berichten en schrijft gegevens naar opslag wanneer de batch een bepaalde grootte bereikt of een bepaalde hoeveelheid tijd is verstreken. IoT Hub standaard voor de volgende bestandsnaamgevingsconventie: 

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

U elke conventie voor bestandsnaamgeving gebruiken, maar u moet alle vermelde tokens gebruiken. IoT Hub schrijft naar een lege blob als er geen gegevens zijn om te schrijven.

We raden u aan de blobs of bestanden op te sommen en er vervolgens overheen te herhalen, om ervoor te zorgen dat alle blobs of bestanden worden gelezen zonder dat er veronderstellingen van partitie worden gemaakt. Het partitiebereik kan mogelijk veranderen tijdens een [door Microsoft geïnitieerde failover-](iot-hub-ha-dr.md#microsoft-initiated-failover) of IoT [Hub-handmatige failover.](iot-hub-ha-dr.md#manual-failover) U de [API Lijstblobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruiken om de lijst met blobs of [Lijst ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) op te sommen voor de lijst met bestanden. Zie het volgende voorbeeld als leidraad.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

> [!NOTE]
> Als uw opslagaccount firewallconfiguraties heeft die de connectiviteit van IoT Hub beperken, u microsoft [vertrouwde first party-uitzondering](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) gebruiken (beschikbaar in bepaalde regio's voor IoT-hubs met beheerde service-identiteit).

Als u een opslagaccount met Azure Data Lake Gen2 wilt maken, maakt u een nieuw V2-opslagaccount en selecteert *u ingeschakeld* op het veld *Hiërarchische naamruimte* op het tabblad **Geavanceerd,** zoals in de volgende afbeelding wordt weergegeven:

![Azure Date Lake Gen2-opslag selecteren](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)


### <a name="service-bus-queues-and-service-bus-topics"></a>Onderwerpen servicebuswachtrijen en servicebus's

Wachtrijen voor servicebussen en onderwerpen die worden gebruikt als Eindpunten van IoT-hub mogen **geen sessies** of **dubbele detectie** hebben ingeschakeld. Als een van deze opties is ingeschakeld, wordt het eindpunt weergegeven als **Onbereikbaar** in de Azure-portal.

> [!NOTE]
> Als uw servicebusbron firewallconfiguraties heeft die de connectiviteit van IoT Hub beperken, u microsoft [vertrouwde first party-uitzondering](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing) gebruiken (beschikbaar in bepaalde regio's voor IoT-hubs met beheerde service-identiteit).


### <a name="event-hubs"></a>Event Hubs

Naast het compatibele eindpunt van de ingebouwde gebeurtenishubs u gegevens ook doorsturen naar aangepaste eindpunten van het type Event Hubs. 

> [!NOTE]
> Als de bron van uw gebeurtenishubs firewallconfiguraties heeft die de connectiviteit van IoT Hub beperken, u microsoft [vertrouwde first party-uitzondering](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) gebruiken (beschikbaar in bepaalde regio's voor IoT-hubs met beheerde service-identiteit).


## <a name="reading-data-that-has-been-routed"></a>Gegevens lezen die zijn gerouteerd

U een route configureren door deze [zelfstudie te volgen.](tutorial-routing.md)

Gebruik de volgende zelfstudies om te leren hoe u berichten van een eindpunt lezen.

* Lezen vanaf [ingebouwde eindpunt](quickstart-send-telemetry-node.md)

* Lezen vanuit [Blob-opslag](../storage/blobs/storage-blob-event-quickstart.md)

* Lezen vanuit [eventhubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lezen vanuit [wachtrijen servicebus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lezen uit [servicebusonderwerpen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)


## <a name="fallback-route"></a>Terugvalroute

De terugvalroute stuurt alle berichten die niet voldoen aan de queryvoorwaarden op een van de bestaande routes naar de ingebouwde gebeurtenishubs **(berichten/gebeurtenissen),** die compatibel zijn met [gebeurtenishubs.](/azure/event-hubs/) Als berichtroutering is ingeschakeld, u de mogelijkheid voor terugvalroute inschakelen. Zodra een route is gemaakt, worden de gegevens niet meer naar het ingebouwde eindpunt geleid, tenzij er een route naar dat eindpunt wordt gemaakt. Als er geen routes naar het ingebouwde eindpunt zijn en een terugvalroute is ingeschakeld, worden alleen berichten verzonden die niet overeenkomen met de queryvoorwaarden op routes naar het ingebouwde eindpunt. Als alle bestaande routes worden verwijderd, moet de terugvalroute ook zijn ingeschakeld om alle gegevens op het ingebouwde eindpunt te ontvangen.

U de terugvalroute in- of uitschakelen in het Azure-portal->Message Routing blade. U Azure Resource Manager voor [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) ook gebruiken om een aangepast eindpunt te gebruiken voor terugvalroute.

## <a name="non-telemetry-events"></a>Niet-telemetriegebeurtenissen

Naast apparaattelemetrie maakt berichtroutering ook het verzenden van apparaattwin change-gebeurtenissen, gebeurtenissen in de levenscyclus van apparaten en gebeurtenissen voor digitale tweelingwijzigingen (in openbare preview) mogelijk. Als er bijvoorbeeld een route wordt gemaakt met gegevensbronset voor **dubbele wijzigingsgebeurtenissen**op het apparaat, stuurt IoT Hub berichten naar het eindpunt dat de wijziging in de apparaattweeling bevat. Als er een route wordt gemaakt met gegevensbronset voor gebeurtenissen in de levenscyclus van **het apparaat,** stuurt IoT Hub een bericht dat aangeeft of het apparaat is verwijderd of gemaakt. Ten slotte kan een ontwikkelaar als onderdeel van de openbare preview van [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md)routes maken met gegevensbronset voor **digitale twin change-gebeurtenissen** en iot-hub verzendt berichten wanneer een digitale dubbele [eigenschap](../iot-pnp/iot-plug-and-play-glossary.md) wordt ingesteld of gewijzigd, een [digitale tweeling](../iot-pnp/iot-plug-and-play-glossary.md) wordt vervangen of wanneer een wijzigingsgebeurtenis plaatsvindt voor de onderliggende apparaattweeling.

[IoT Hub integreert ook met Azure Event Grid](iot-hub-event-grid.md) om apparaatgebeurtenissen te publiceren ter ondersteuning van realtime integraties en automatisering van workflows op basis van deze gebeurtenissen. Bekijk de belangrijkste [verschillen tussen berichtroutering en gebeurtenisraster](iot-hub-event-grid-routing-comparison.md) om te leren welke het beste werkt voor uw scenario.

## <a name="testing-routes"></a>Testroutes

Wanneer u een nieuwe route maakt of een bestaande route bewerkt, moet u de routequery testen met een voorbeeldbericht. U afzonderlijke routes testen of alle routes tegelijk testen en er worden geen berichten naar de eindpunten gerouteerd tijdens de test. Azure-portal, Azure Resource Manager, Azure PowerShell en Azure CLI kunnen worden gebruikt voor het testen. Resultaten helpen te bepalen of het voorbeeldbericht overeenkomt met de query, het bericht niet overeenkwam met de query of dat de test niet kan worden uitgevoerd omdat het voorbeeldbericht of de syntaxis van query's onjuist zijn. Zie [Route testen](/rest/api/iothub/iothubresource/testroute) en [Alle routes testen](/rest/api/iothub/iothubresource/testallroutes)voor meer informatie.

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Bestelgaranties met ten minste eenmaal levering

IoT Hub-berichtroutering garandeert geordend en ten minste eenmaal levering van berichten naar de eindpunten. Dit betekent dat er dubbele berichten kunnen zijn en dat een reeks berichten opnieuw kan worden verzonden ter ere van het oorspronkelijke bericht dat het bestellen van berichten wordt verzonden. Als de oorspronkelijke berichtvolgorde bijvoorbeeld [1,2,3,4] is, u een berichtreeks ontvangen zoals [1,2,1,2,3,1,2,3,4]. De bestelgarantie is dat als u ooit bericht [1] ontvangt, het altijd gevolgd zou worden door [2,3,4].

Voor het verwerken van berichtduplicaten raden we u aan een unieke id te stempelen in de toepassingseigenschappen van het bericht op het punt van oorsprong, dat meestal een apparaat of een module is. De service die de berichten verbruikt, kan dubbele berichten verwerken met deze id.

## <a name="latency"></a>Latentie

Wanneer u telemetrieberichten van apparaat naar cloud routeert met ingebouwde eindpunten, is er een lichte toename van de end-to-end latentie na het maken van de eerste route.

In de meeste gevallen is de gemiddelde toename van de latentie minder dan 500 ms. U de latentie controleren met **routering: berichtlatentie voor berichten/gebeurtenissen** of **d2c.endpoints.latency.builtIn.events** IoT Hub metric. Het maken of verwijderen van een route na de eerste heeft geen invloed op de end-to-end latentie.

## <a name="monitoring-and-troubleshooting"></a>Monitoring en probleemoplossing

IoT Hub biedt verschillende statistieken met betrekking tot routering en eindpunten om u een overzicht te geven van de status van uw hub en verzonden berichten. U informatie uit meerdere statistieken combineren om de hoofdoorzaak voor problemen te identificeren. Gebruik bijvoorbeeld metrische **routering: telemetrieberichten die zijn verwijderd** of **d2c.telemetry.egress.dropped** om het aantal berichten te identificeren dat is verwijderd wanneer ze niet overeenkomen met query's op een van de routes en de terugvalroute is uitgeschakeld. [IoT Hub-statistieken](iot-hub-metrics.md) bevatten alle statistieken die standaard zijn ingeschakeld voor uw IoT-hub.

U de REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken om de [status](iot-hub-devguide-endpoints.md#custom-endpoints) van de eindpunten te krijgen. We raden u aan de [IoT Hub-statistieken](iot-hub-metrics.md) met betrekking tot de latentie van routeringsberichten te gebruiken om fouten te identificeren en te debuggen wanneer de status van eindpunt dood of ongezond is. Voor gebeurtenishubs voor eindpunttypen u bijvoorbeeld **d2c.endpoints.latency.eventHubs**controleren. De status van een ongezond eindpunt wordt bijgewerkt naar gezond wanneer IoT Hub een uiteindelijk consistente gezondheidstoestand heeft vastgesteld.

Met behulp van de diagnostische logboeken **van routes** in de [diagnostische instellingen](../iot-hub/iot-hub-monitor-resource-health.md)van Azure Monitor u fouten bijhouden die optreden tijdens de evaluatie van een routeringsquery en endpoint-status zoals waargenomen door IoT Hub, bijvoorbeeld wanneer een eindpunt is dood. Deze diagnostische logboeken kunnen worden verzonden naar Azure Monitor-logboeken, gebeurtenishubs of Azure Storage voor aangepaste verwerking.

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-berichten](tutorial-routing.md)verwerken met routes voor meer informatie over het maken van berichtenroutes.

* [Apparaat-naar-cloudberichten verzenden](quickstart-send-telemetry-node.md)

* Zie [Azure IoT SDKs](iot-hub-devguide-sdks.md)voor informatie over de SDK's die u gebruiken om device-to-cloudberichten te verzenden.
