---
title: Azure IoT Hub-schaling | Microsoft Documenten
description: Hoe u uw IoT-hub schalen om uw verwachte berichtdoorvoer en de gewenste functies te ondersteunen. Bevat een overzicht van de ondersteunde doorvoer voor elke laag en opties voor sharding.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ecc1ae5138fe5a1e42fed9be2e31b5afa8b6d5b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497500"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>De juiste IoT Hub-laag kiezen voor uw oplossing

Elke IoT-oplossing is anders, dus Azure IoT Hub biedt verschillende opties op basis van prijzen en schaal. Dit artikel is bedoeld om u te helpen uw IoT Hub-behoeften te evalueren. Zie [IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub)voor prijsinformatie over IoT Hub-lagen.

Als u wilt beslissen welke IoT Hub-laag geschikt is voor uw oplossing, stelt u zich twee vragen:

**Welke functies ben ik van plan te gebruiken?**

Azure IoT Hub biedt twee niveaus, basis en standaard, die verschillen in het aantal functies dat ze ondersteunen. Als uw IoT-oplossing is gebaseerd op het verzamelen van gegevens van apparaten en het centraal analyseren ervan, dan is de basislaag waarschijnlijk geschikt voor u. Als u geavanceerdere configuraties wilt gebruiken om IoT-apparaten op afstand te bedienen of een deel van uw workloads op de apparaten zelf te distribueren, moet u rekening houden met de standaardlaag. Voor een gedetailleerde uitsplitsing van welke functies zijn opgenomen in elke laag blijven [basic en standaard lagen](#basic-and-standard-tiers).

**Hoeveel gegevens ben ik van plan om dagelijks te verplaatsen?**

Elke IoT Hub-laag is beschikbaar in drie formaten, gebaseerd op hoeveel gegevensdoorvoer ze op een bepaalde dag aankunnen. Deze maten worden numeriek geïdentificeerd als 1, 2 en 3. Elke eenheid van een Level 1 IoT-hub kan bijvoorbeeld 400 duizend berichten per dag verwerken, terwijl een level 3-eenheid 300 miljoen kan verwerken. Ga voor meer informatie over de gegevensrichtlijnen verder naar [Berichtdoorvoer](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Basis- en standaardlagen

De standaardlaag van IoT Hub maakt alle functies mogelijk en is vereist voor alle IoT-oplossingen die gebruik willen maken van de bidirectionele communicatiemogelijkheden. Met de Basic-servicelaag beschikt u over een subset van de functies. Dit is bedoeld voor IoT-oplossingen waarvoor u alleen communicatie in één richting (van apparaten naar de cloud) nodig hebt. Beide servicelagen bieden dezelfde beveiligings- en verificatiefuncties.

Per IoT Hub kan slechts één type [editie](https://azure.microsoft.com/pricing/details/iot-hub/) binnen een laag worden gekozen. U bijvoorbeeld een IoT-hub maken met meerdere eenheden S1, maar niet met een mix van eenheden uit verschillende edities, zoals S1 en S2.

| Mogelijkheid | Basislaag | Gratis/standaardlaag |
| ---------- | ---------- | ------------- |
| [Apparaat-naar-cloud telemetrie](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Identiteit per apparaat](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Berichtroutering,](iot-hub-devguide-messages-read-custom.md) [berichtverrijkingen](iot-hub-message-enrichments-overview.md)en [integratie van gebeurtenisraster](iot-hub-event-grid.md) | Ja | Ja |
| [HTTP-, AMQP- en MQTT-protocollen](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Service voor inrichting van apparaten](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Bewaking en diagnose](iot-hub-monitor-resource-health.md) | Ja | Ja |
| [Cloud-to-device messaging](iot-hub-devguide-c2d-guidance.md) |   | Ja |
| [De tweelingen](iot-hub-devguide-device-twins.md)van het apparaat, [De tweelingen van](iot-hub-devguide-module-twins.md)de module, en [Het beheer van het apparaat](iot-hub-device-management-overview.md) |   | Ja |
| [Apparaatstreams (voorbeeld)](iot-hub-device-streams-overview.md) |   | Ja |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ja |
| [Voorbeeld van IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Ja |

IoT Hub biedt ook een gratis laag die bedoeld is voor testen en evaluatie. Het heeft alle mogelijkheden van de standaardlaag, maar beperkte messaging-emissierechten. U niet upgraden van de gratis laag naar basis- of standaardniveau.

## <a name="partitions"></a>Partities

Azure IoT-hubs bevatten veel kerncomponenten van [Azure Event Hubs,](../event-hubs/event-hubs-features.md)waaronder [partities.](../event-hubs/event-hubs-features.md#partitions) Gebeurtenisstreams voor IoT-hubs worden over het algemeen gevuld met binnenkomende telemetriegegevens die worden gerapporteerd door verschillende IoT-apparaten. De partitionering van de gebeurtenisstream wordt gebruikt om stellingen te verminderen die optreden bij het gelijktijdig lezen en schrijven naar gebeurtenisstreams.

De partitielimiet wordt gekozen wanneer IoT Hub wordt gemaakt en kan niet worden gewijzigd. De maximale partitielimiet voor iot-hub met basisniveau en iot-hub met standaardniveau is 32. De meeste IoT-hubs hebben slechts 4 partities nodig. Zie de veelgestelde vragen over gebeurtenishubs [Hoeveel partities heb ik nodig voor](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need) meer informatie over het bepalen van de partities?

## <a name="tier-upgrade"></a>Tier-upgrade

Zodra u uw IoT-hub hebt gemaakt, u upgraden van de basislaag naar de standaardlaag zonder uw bestaande bewerkingen te onderbreken. Zie [Uw IoT-hub upgraden voor](iot-hub-upgrade.md)meer informatie.

De partitieconfiguratie blijft ongewijzigd wanneer u migreert van basislaag naar standaardlaag.

> [!NOTE]
> De gratis laag biedt geen ondersteuning voor upgraden naar basis of standaard.

## <a name="iot-hub-rest-apis"></a>IOT Hub REST API's

Het verschil in ondersteunde mogelijkheden tussen de basis- en standaardlagen van IoT Hub betekent dat sommige API-aanroepen niet werken met basisniveauhubs. In de volgende tabel ziet u welke API's beschikbaar zijn:

| API | Basislaag | Gratis/standaardlaag |
| --- | ---------- | ------------- |
| [Apparaat verwijderen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Ja | Ja |
| [Apparaat ontvangen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Ja | Ja |
| [Module verwijderen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Ja | Ja |
| [Module ontvangen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Ja | Ja |
| [Registerstatistieken bekijken](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Ja | Ja |
| [Servicesstatistieken bekijken](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Ja | Ja |
| [Apparaat maken of bijwerken](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Ja | Ja |
| [Module maken of bijwerken](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Ja | Ja |
| [IoT-hub opvragen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Ja | Ja |
| [Bestanden uploaden SAS URI maken](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ja | Ja |
| [Melding van apparaatgebonden ontvangen](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ja | Ja |
| [Apparaatgebeurtenis verzenden](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ja | Ja |
| Modulegebeurtenis verzenden | ALLEEN AMQP en MQTT | ALLEEN AMQP en MQTT |
| [Status van het uploaden van bestanden bijwerken](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ja | Ja |
| [Bewerking bulkapparaat](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Ja, behalve iot edge-mogelijkheden | Ja |
| [Importexporttaak annuleren](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Ja | Ja |
| [Importexporttaak maken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Ja | Ja |
| [Exporttaak importeren zoeken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Ja | Ja |
| [Importexporttaken zoeken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Ja | Ja |
| [Opdrachtwachtrij wissen](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Ja |
| [Apparaattwee krijgen](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Ja |
| [Moduletwin krijgen](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Ja |
| [Apparaatmethode aanroepen](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Ja |
| [Apparaattweeling bijwerken](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Ja |
| [Moduletwin bijwerken](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Ja |
| [Melding van apparaatgebonden beveiliging verlaten](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ja |
| [Melding van apparaatgebonden apparaten voltooien](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ja |
| [Taak annuleren](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Ja |
| [Taak maken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Ja |
| [Krijg werk](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Ja |
| [Querytaken](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Ja |

## <a name="message-throughput"></a>Berichtdoorvoer

De beste manier om een IoT Hub-oplossing te vergroten, is door het verkeer per eenheid te evalueren. Houd met name rekening met de vereiste piekdoorvoer voor de volgende categorieën bewerkingen:

* Apparaat-naar-cloud-berichten
* Cloud-to-device berichten
* Registerbewerkingen voor identiteit

Het verkeer wordt per eenheid gemeten voor uw IoT-hub. Wanneer u een IoT-hub maakt, kiest u de laag en de uitvoering ervan en stelt u het aantal beschikbare eenheden in. U maximaal 200 eenheden kopen voor de B1-, B2-, S1- of S2-editie, of maximaal 10 eenheden voor de B3- of S3-editie. Nadat uw IoT-hub is gemaakt, u het aantal beschikbare eenheden binnen de uitvoering wijzigen, upgraden of downgraden tussen edities binnen de laag (B1 naar B2) of upgraden van de basisnaar de standaardlaag (B1 naar S1) zonder uw bestaande activiteiten te onderbreken. Zie [Uw IoT-hub upgraden voor](iot-hub-upgrade.md)meer informatie.  

Als voorbeeld van de verkeersmogelijkheden van elke laag volgen device-to-cloud-berichten de volgende richtlijnen voor langdurige doorvoer:

| Tier-editie | Aanhoudende doorvoer | Aanhoudende verzendsnelheid |
| --- | --- | --- |
| B1, S1 |Maximaal 1111 KB/minuut per eenheid<br/>(1,5 GB/dag/eenheid) |Gemiddeld 278 berichten per minuut per eenheid<br/>(400.000 berichten per dag per eenheid) |
| B2, S2 |Maximaal 16 MB/minuut per eenheid<br/>(22,8 GB/dag/eenheid) |Gemiddeld 4.167 berichten per minuut per eenheid<br/>(6 miljoen berichten per dag per eenheid) |
| B3, S3 |Maximaal 814 MB/minuut per eenheid<br/>(1144,4 GB/dag/eenheid) |Gemiddeld 208.333 berichten per minuut per eenheid<br/>(300 miljoen berichten per dag per eenheid) |

De doorvoer van apparaat naar cloud is slechts een van de statistieken waar u rekening mee moet houden bij het ontwerpen van een IoT-oplossing. Zie [IoT Hub-quota en throttles](iot-hub-devguide-quotas-throttling.md)voor meer uitgebreide informatie.

### <a name="identity-registry-operation-throughput"></a>Doorvoer van identiteitsregisterbewerking

IoT Hub-identiteitsregisterbewerkingen zijn niet bedoeld als run-time-bewerkingen, omdat ze meestal verband houden met het inrichten van apparaten.

Zie [IoT Hub-quota en throttles](iot-hub-devguide-quotas-throttling.md)voor specifieke burst-prestatienummers.

## <a name="auto-scale"></a>Automatisch schalen

Als u de toegestane berichtlimiet op uw IoT-hub nadert, u deze stappen gebruiken [om automatisch te schalen](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) om een IoT Hub-eenheid in dezelfde IoT Hub-laag te verhogen.

## <a name="next-steps"></a>Volgende stappen

* Zie [IoT Hub-prijzen](https://azure.microsoft.com/pricing/details/iot-hub) of [IoT Hub-quota en throttles](iot-hub-devguide-quotas-throttling.md)voor meer informatie over iot-hubmogelijkheden en prestatiedetails.

* Als u de laag IoT-hub wilt wijzigen, voert u de stappen uit in [Uw IoT-hub upgraden.](iot-hub-upgrade.md)
