---
title: Meer informatie over Azure IoT Hub Message Routing | Microsoft Docs
description: 'Ontwikkelaars handleiding: berichten routering gebruiken om apparaat-naar-Cloud-berichten te verzenden. Bevat informatie over het verzenden van zowel telemetrie-als niet-telemetrie-gegevens.'
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 6ee9e334c10bd2d0f291b5fd1bb547ba3ba83ddb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877188"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub bericht routering gebruiken om apparaat-naar-Cloud-berichten te verzenden naar verschillende eind punten

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Met bericht routering kunt u berichten van uw apparaten naar Cloud Services verzenden in een geautomatiseerde, schaal bare en betrouw bare manier. Bericht routering kan worden gebruikt voor: 

* Het verzenden van telemetrie- **telefoon berichten en gebeurtenissen** , namelijk levenscyclus gebeurtenissen van apparaten en gebeurtenissen voor het wijzigen van het apparaat naar het ingebouwde eind punt en aangepaste eind punten. Meer informatie over [Routing-eind punten](#routing-endpoints).

* **Gegevens filteren voordat deze naar verschillende eind punten worden doorgestuurd** door uitgebreide query's toe te passen. Met bericht routering kunt u een query uitvoeren op de bericht eigenschappen en de bericht tekst, evenals dubbele labels voor apparaten en dubbele eigenschappen van het apparaat. Meer informatie over het gebruik [van query's in bericht routering](iot-hub-devguide-routing-query-syntax.md).

IoT Hub moet schrijf toegang hebben tot deze service-eind punten voor het werken met bericht routering. Als u uw eind punten via de Azure Portal configureert, worden de benodigde machtigingen voor u toegevoegd. Zorg ervoor dat u uw services zo configureert dat de verwachte door Voer wordt ondersteund. Wanneer u uw IoT-oplossing voor het eerst configureert, moet u mogelijk de extra eind punten controleren en alle benodigde aanpassingen voor de werkelijke belasting aanbrengen.

Met de IoT Hub definieert u een [algemene indeling](iot-hub-devguide-messages-construct.md) voor alle apparaat-naar-Cloud-berichten voor interoperabiliteit in verschillende protocollen. Als een bericht overeenkomt met meerdere routes die naar hetzelfde eind punt wijzen, wordt IoT Hub alleen een bericht voor dat eind punt bezorgd. Daarom hoeft u geen ontdubbeling te configureren voor uw Service Bus wachtrij of onderwerp. In gepartitioneerde wacht rijen, wordt de rang orde van berichten door partitie-affiniteit gegarandeerd. Gebruik deze zelf studie voor meer informatie over het [configureren van bericht routering](tutorial-routing.md).

## <a name="routing-endpoints"></a>Routerings eindpunten

Een IoT-hub heeft een standaard ingebouwde eind punt (**berichten/gebeurtenissen**) die compatibel is met Event hubs. U kunt [aangepaste eind punten](iot-hub-devguide-endpoints.md#custom-endpoints) maken om berichten naar te sturen door andere services in uw abonnement te koppelen aan de IOT hub. IoT Hub ondersteunt momenteel de volgende services als aangepaste eind punten:

### <a name="built-in-endpoint"></a>Ingebouwd eind punt

U kunt standaard [Event hubs integratie en sdk's](iot-hub-devguide-messages-read-builtin.md) gebruiken om apparaat-naar-Cloud-berichten te ontvangen van het ingebouwde eind punt (**berichten/gebeurtenissen**). Zodra een route is gemaakt, stopt de gegevens naar het ingebouwde eind punt, tenzij er een route naar dat eind punt wordt gemaakt.

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub ondersteunt het schrijven van gegevens naar Azure Blob Storage in de [Apache Avro](https://avro.apache.org/) -indeling en in de JSON-indeling. De mogelijkheid om de JSON-indeling te coderen, is algemeen beschikbaar in alle regio's waar IoT Hub beschikbaar is. De standaard waarde is AVRO. De coderings indeling kan alleen worden ingesteld wanneer het eind punt van de Blob-opslag is geconfigureerd. De indeling kan niet worden bewerkt voor een bestaand eind punt. Wanneer u JSON-code ring gebruikt, moet u het content type instellen op **Application/JSON** en ContentEncoding naar **UTF-8** in de eigenschappen van het bericht [systeem](iot-hub-devguide-routing-query-syntax.md#system-properties). Beide waarden zijn niet hoofdletter gevoelig. Als de inhouds codering niet is ingesteld, schrijft IoT Hub de berichten in de indeling basis 64-code ring. U kunt de coderings indeling selecteren met behulp van de IoT Hub REST API maken of bijwerken, met name de [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), de Azure Portal, de [Azure cli](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)of de [Azure Power shell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0). In het volgende diagram ziet u hoe u de coderings indeling kunt selecteren in de Azure Portal.

![Eindpunt codering van Blob-opslag](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub biedt ook ondersteuning voor route ring van berichten naar ADLS Gen2-accounts. Dit zijn hiërarchische opslag accounts met [naam ruimten](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)die boven op de Blob-opslag zijn gebouwd. Deze mogelijkheid is in open bare preview en beschikbaar voor nieuwe ADLS Gen2 accounts in VS-West 2 en West-Centraal vs. Deze mogelijkheid wordt binnenkort geïmplementeerd in alle Cloud regio's.

IoT Hub batch berichten en schrijft gegevens naar een BLOB wanneer de batch een bepaalde grootte bereikt of een bepaalde hoeveelheid tijd is verstreken. IoT Hub wordt standaard ingesteld op de volgende bestands naam Conventie:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

U kunt elke bestands naam Conventie gebruiken, maar u moet alle vermelde tokens gebruiken. IoT Hub wordt naar een lege BLOB geschreven als er geen gegevens zijn om te schrijven.

Bij het door sturen naar Blob-opslag wordt aangeraden om de blobs in te schrijven en vervolgens te herhalen, om ervoor te zorgen dat alle containers worden gelezen zonder dat er aannames van de partitie worden gemaakt. Het partitie bereik kan mogelijk worden gewijzigd tijdens een door [micro soft geïnitieerde failover](iot-hub-ha-dr.md#microsoft-initiated-failover) of IOT hub [hand matige failover](iot-hub-ha-dr.md#manual-failover). U kunt de [List blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) gebruiken om de lijst met blobs op te sommen. Raadpleeg het volgende voor beeld als richt lijn.

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

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus-wacht rijen en Service Bus onderwerpen

Service Bus-wacht rijen en-onderwerpen die als IoT Hub-eind punten worden gebruikt, mogen geen **sessies** of **Duplicaten detectie** zijn ingeschakeld. Als een van deze opties is ingeschakeld, wordt het eind punt weer gegeven als onbereikbaar in de Azure Portal.

### <a name="event-hubs"></a>Event Hubs

Naast het ingebouwde Event Hubs compatibele eind punt, kunt u ook gegevens routeren naar aangepaste eind punten van het type Event Hubs. 

## <a name="reading-data-that-has-been-routed"></a>Gegevens lezen die zijn doorgestuurd

U kunt een route configureren door deze [zelf studie](tutorial-routing.md)te volgen.

Gebruik de volgende zelf studies voor meer informatie over het lezen van een bericht vanuit een eind punt.

* Lezen van [ingebouwd eind punt](quickstart-send-telemetry-node.md)

* Lezen uit [Blob Storage](../storage/blobs/storage-blob-event-quickstart.md)

* Lezen van [Event hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lezen van [Service Bus wachtrijen](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lezen uit [Service Bus onderwerpen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Terugval route

De terugval route verzendt alle berichten die niet voldoen aan de query voorwaarden voor een van de bestaande routes naar de ingebouwde Event Hubs (**berichten/gebeurtenissen**) die compatibel zijn met [Event hubs](/azure/event-hubs/). Als bericht routering is ingeschakeld, kunt u de mogelijkheid om de terugval route te gebruiken in te scha kelen. Zodra een route is gemaakt, stopt de gegevens stroom naar het ingebouwde eind punt, tenzij er een route naar dat eind punt wordt gemaakt. Als er geen routes naar het ingebouwde eind punt en een terugval route zijn ingeschakeld, worden alleen berichten die niet overeenkomen met de query voorwaarden op routes, verzonden naar het ingebouwde eind punt. Als alle bestaande routes worden verwijderd, moet er ook een terugval route worden ingeschakeld om alle gegevens bij het ingebouwde eind punt te ontvangen.

U kunt de terugval route in-of uitschakelen op de Blade voor de Azure Portal-> bericht routering. U kunt Azure Resource Manager ook gebruiken voor [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om een aangepast eind punt voor terugval route te gebruiken.

## <a name="non-telemetry-events"></a>Niet-telemetrie-gebeurtenissen

Naast de telemetrie van apparaten kunnen ook bericht routering het verzenden van dubbele wijzigings gebeurtenissen, levens cyclus gebeurtenissen en gebeurtenissen voor digitale dubbele wijzigingen (in open bare preview). Als er bijvoorbeeld een route is gemaakt met een gegevens bron die is ingesteld op **dubbele wijzigings gebeurtenissen**van het apparaat, IOT hub stuurt berichten naar het eind punt dat de wijziging in het dubbele apparaat bevat. Als er een route is gemaakt met een gegevens bron die is ingesteld op **levens cyclus gebeurtenissen van apparaten**, IOT hub een bericht verzenden dat aangeeft of het apparaat is verwijderd of gemaakt. Ten slotte kan een ontwikkelaar, als onderdeel van de [open bare preview van IoT Plug en Play](../iot-pnp/overview-iot-plug-and-play.md), routes maken met een gegevens bron die is ingesteld op **digitale dubbele wijzigings gebeurtenissen** en IOT hub berichten verzendt wanneer een digitale dubbele [eigenschap](../iot-pnp/iot-plug-and-play-glossary.md) is ingesteld of gewijzigd, een [digitale dubbele ](../iot-pnp/iot-plug-and-play-glossary.md)wordt vervangen of wanneer een wijzigings gebeurtenis plaatsvindt voor het onderliggende apparaat dubbele.

[IOT hub integreert ook met Azure Event grid](iot-hub-event-grid.md) voor het publiceren van faxgebeurtenissen om realtime integraties en automatisering van werk stromen te ondersteunen op basis van deze gebeurtenissen. Bekijk de belangrijkste [verschillen tussen bericht Routering en Event grid](iot-hub-event-grid-routing-comparison.md) om te leren welke het beste werkt voor uw scenario.

## <a name="testing-routes"></a>Routes testen

Wanneer u een nieuwe route maakt of een bestaande route bewerkt, moet u de route query testen met een voorbeeld bericht. U kunt afzonderlijke routes testen of alle routes tegelijk testen en er tijdens de test geen berichten naar de eind punten worden doorgestuurd. Azure Portal, Azure Resource Manager, Azure PowerShell en Azure CLI kunnen worden gebruikt voor het testen. Resultaten helpen te bepalen of het voorbeeld bericht overeenkomt met de query. het bericht komt niet overeen met de query of de test kan niet worden uitgevoerd omdat het voorbeeld bericht of de query syntaxis onjuist is. Zie [route ring testen](/rest/api/iothub/iothubresource/testroute) en [alle routes testen](/rest/api/iothub/iothubresource/testallroutes)voor meer informatie.

## <a name="latency"></a>Latentie

Wanneer u apparaat-naar-Cloud-telemetrie-berichten rondstuurt met ingebouwde eind punten, is er een lichte toename van de end-to-end-latentie na het maken van de eerste route.

In de meeste gevallen is de gemiddelde toename in latentie kleiner dan 500 MS. U kunt de latentie bewaken met behulp **van route ring: bericht latentie voor berichten/gebeurtenissen** of **D2C. endpoints. latentie. builtIn. events** IOT hub metrische gegevens. Het maken of verwijderen van een route na de eerste heeft geen invloed op de end-to-end-latentie.

## <a name="monitoring-and-troubleshooting"></a>Bewaking en probleem oplossing

IoT Hub biedt diverse metrische gegevens met betrekking tot route ring en eind punten om u een overzicht te geven van de status van uw hub en verzonden berichten. U kunt informatie uit meerdere metrische gegevens combi neren om de hoofd oorzaak van problemen vast te stellen. Gebruik bijvoorbeeld metrische **route ring: telemetriegegevens** die zijn neergezet of **D2C.** telemetrie. dropd om het aantal berichten te identificeren dat is verwijderd toen de query's op een van de routes niet overeenkomen en de terugval route is uitgeschakeld. Met [IOT hub metrische gegevens](iot-hub-metrics.md) worden alle metrische gegevens weer gegeven die standaard zijn ingeschakeld voor uw IOT hub.

U kunt de REST API status van [eind punt ophalen](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) gebruiken [](iot-hub-devguide-endpoints.md#custom-endpoints) om de status van de eind punten op te halen. U kunt het beste de [IOT hub metrische gegevens](iot-hub-metrics.md) met betrekking tot de bericht latentie van de route ring gebruiken om fouten op te sporen en op te sporen wanneer de status van het eind punt dood of slecht is. U kunt bijvoorbeeld voor het type eind punt Event Hubs **D2C. endpoints. latentie. Event hubs**. De status van een onjuist eind punt wordt bijgewerkt naar in orde wanneer IoT Hub een uiteindelijk consistente status van de status heeft bereikt.

Met behulp van de **routes** Diagnostische logboeken in azure monitor [Diagnostische instellingen](../iot-hub/iot-hub-monitor-resource-health.md)kunt u fouten volgen die optreden tijdens de evaluatie van een routerings query en de status van een eind punt, zoals wordt waargenomen door IOT hub, bijvoorbeeld wanneer een eind punt dood is. Deze Diagnostische logboeken kunnen worden verzonden naar Azure Monitor-logboeken, Event Hubs of Azure Storage voor aangepaste verwerking.

## <a name="next-steps"></a>Volgende stappen

* Zie [IOT hub apparaat-naar-Cloud-berichten verwerken met routes](tutorial-routing.md)voor meer informatie over het maken van bericht routes.

* [Apparaat-naar-Cloud-berichten verzenden](quickstart-send-telemetry-node.md)

* Zie [Azure IOT sdk's](iot-hub-devguide-sdks.md)voor informatie over de sdk's die u kunt gebruiken om apparaat-naar-Cloud-berichten te verzenden.
